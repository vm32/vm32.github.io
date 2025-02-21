**Hiding Data in GPU VRAM Using Direct3D 11: A Technical Exploration**

### **Introduction**

Modern GPUs are not just powerful computational devices for rendering graphics but also house substantial memory (VRAM) that can be utilized in unconventional ways. One such intriguing use case is storing and retrieving data in GPU VRAM---potentially for stealthy operations. This article delves into a simple yet effective method of moving data into and out of GPU memory using Direct3D 11.

### **Understanding the Concept**

The primary idea behind this technique is to allocate buffers in GPU VRAM, write arbitrary data into them, and later retrieve it. Since many forensic tools focus on system RAM rather than VRAM, hiding sensitive data in GPU memory could provide a layer of obfuscation against common memory analysis techniques.

This approach utilizes Direct3D 11 APIs to:

-   Allocate memory on the GPU.
-   Move data from system memory into GPU memory.
-   Retrieve data from GPU memory and return it to system memory.

### **Breaking Down the Code**

Let's analyze the core components of the implementation.

**1\. Custom Memory Copy Function**

```cpp
PVOID ImplCopyMemory(_Inout_ PVOID Destination, _In_ CONST PVOID Source, _In_ SIZE_T Length)\
{\
    PBYTE D = (PBYTE)Destination;\
    PBYTE S = (PBYTE)Source;

    while (Length--)\
        *D++ = *S++;

    return Destination;\
}
```

This function performs a manual memory copy, mimicking `memcpy()`. While unnecessary in modern applications, it could be useful in scenarios where built-in functions are flagged by security software.

**2\. Allocating GPU Memory Buffers**

```cpp
ID3D11Buffer* AllocateD3d11Memory(ID3D11Device* Device, DWORD Size, BOOL ReadbackRoutine)\
{\
    D3D11_BUFFER_DESC Description = {0};\
    ID3D11Buffer* Buffer = NULL;

    Description.ByteWidth = Size;\
    Description.Usage = ReadbackRoutine ? D3D11_USAGE_STAGING : D3D11_USAGE_DEFAULT;\
    Description.CPUAccessFlags = ReadbackRoutine ? D3D11_CPU_ACCESS_READ : 0;\
    Description.BindFlags = ReadbackRoutine ? 0 : D3D11_BIND_UNORDERED_ACCESS;

    if (Device->CreateBuffer(&Description, NULL, &Buffer) != S_OK)\
        return NULL;

    return Buffer;\
}
```
This function dynamically allocates memory in the GPU based on the intended use case:

-   **`D3D11_USAGE_DEFAULT`**: Used for standard GPU memory allocation where the CPU doesn't have direct access.
-   **`D3D11_USAGE_STAGING`**: Allows CPU access for reading the buffer.

**3\. Moving Data to GPU Memory**

```cpp
BOOL Id3d11MoveMemoryToGPUMemory(ID3D11DeviceContext* Context, ID3D11Buffer* DataIn, PVOID Buffer)\
{\
    if (Context)\
        Context->UpdateSubresource(DataIn, 0, NULL, Buffer, 0, 0);

    return TRUE;\
}
```

Here, `UpdateSubresource()` writes the given data from system memory to the GPU buffer.

**4\. Retrieving Data from GPU Memory**
```cpp
BOOL Id3d11PullMemoryFromGPUMemory(ID3D11DeviceContext* Context, ID3D11Buffer* DataIn, ID3D11Buffer* DataOut, PVOID Buffer, SIZE_T BufferSize)\
{\
    D3D11_MAPPED_SUBRESOURCE Mapped = { 0 };\
    HRESULT Result = S_OK;\
    BOOL bFlag = FALSE;

    if (!DataIn || !DataOut)\
        goto EXIT_ROUTINE;

    Context->CopyResource(DataOut, DataIn);

    Result = Context->Map(DataOut, 0, D3D11_MAP_READ, 0, &Mapped);\
    if (!SUCCEEDED(Result))\
        goto EXIT_ROUTINE;

    bFlag = TRUE;

    ImplCopyMemory(Buffer, Mapped.pData, BufferSize);

EXIT_ROUTINE:

    if (bFlag)\
        Context->Unmap(DataOut, 0);

    return bFlag;\
}
```

This function first copies the GPU buffer (`DataIn`) to a **staging buffer** (`DataOut`) that has CPU read access. Then, it maps this buffer and copies the data back to system memory.

**5\. Execution Flow in `main()`**

```cpp
INT main(VOID)\
{\
    HRESULT Result = S_OK;

    ID3D11Device* Device = NULL;\
    ID3D11DeviceContext* Context = NULL;

    ID3D11Buffer* In = NULL;\
    ID3D11Buffer* Out = NULL;

    BYTE Buffer[10] = "GPU stash"; 
    BYTE TryBuffer[10] = { 0 };

    Result = D3D11CreateDevice(NULL, D3D_DRIVER_TYPE_HARDWARE, NULL, 0, NULL, 0, D3D11_SDK_VERSION, &Device, NULL, &Context);\
    if (!SUCCEEDED(Result))\
        goto EXIT_ROUTINE;

    In = AllocateD3d11Memory(Device, ARRAY_SIZE(Buffer), FALSE);\
    if (In == NULL)\
        goto EXIT_ROUTINE;

    Out = AllocateD3d11Memory(Device, ARRAY_SIZE(Buffer), TRUE);\
    if (Out == NULL)\
        goto EXIT_ROUTINE;

    Id3d11MoveMemoryToGPUMemory(Context, In, Buffer);

    Id3d11PullMemoryFromGPUMemory(Context, In, Out, TryBuffer, ARRAY_SIZE(TryBuffer));

EXIT_ROUTINE:

    if (In)\
        In->Release();

    if (Out)\
        Out->Release();

    if (Device)\
        Device->Release();

    if (Context)\
        Context->Release();

    return ERROR_SUCCESS;\
}
```
# Full code 
```cpp
#include <Windows.h>
#include <d3d11.h>
#pragma comment(lib, "d3d11.lib")

#define ARRAY_SIZE(arr) (sizeof(arr) / sizeof((arr)[0]))

PVOID ImplCopyMemory(_Inout_ PVOID Destination, _In_ CONST PVOID Source, _In_ SIZE_T Length)
{
    PBYTE D = (PBYTE)Destination;
    PBYTE S = (PBYTE)Source;

    while (Length--)
        *D++ = *S++;

    return Destination;
}

BOOL Id3d11PullMemoryFromGPUMemory(ID3D11DeviceContext* Context, ID3D11Buffer* DataIn, ID3D11Buffer* DataOut, PVOID Buffer, SIZE_T BufferSize)
{
    D3D11_MAPPED_SUBRESOURCE Mapped = { 0 };
    HRESULT Result = S_OK;
    BOOL bFlag = FALSE;

    if (!DataIn || !DataOut)
        goto EXIT_ROUTINE;
    
    Context->CopyResource(DataOut, DataIn);

    Result = Context->Map(DataOut, 0, D3D11_MAP_READ, 0, &Mapped);
    if (!SUCCEEDED(Result))
        goto EXIT_ROUTINE;

    bFlag = TRUE;

    ImplCopyMemory(Buffer, Mapped.pData, BufferSize);

EXIT_ROUTINE:

    if (bFlag)
        Context->Unmap(DataOut, 0);

    return bFlag;
}

BOOL Id3d11MoveMemoryToGPUMemory(ID3D11DeviceContext* Context, ID3D11Buffer* DataIn, PVOID Buffer)
{
    if (Context)
        Context->UpdateSubresource(DataIn, 0, NULL, Buffer, 0, 0);

    return TRUE;
}

ID3D11Buffer* AllocateD3d11Memory(ID3D11Device* Device, DWORD Size, BOOL ReadbackRoutine)
{
    D3D11_BUFFER_DESC Description = {0};
    ID3D11Buffer* Buffer = NULL;

    Description.ByteWidth = Size;
    Description.Usage = ReadbackRoutine ? D3D11_USAGE_STAGING : D3D11_USAGE_DEFAULT;
    Description.CPUAccessFlags = ReadbackRoutine ? D3D11_CPU_ACCESS_READ : 0;
    Description.BindFlags = ReadbackRoutine ? 0 : D3D11_BIND_UNORDERED_ACCESS;

    if (Device->CreateBuffer(&Description, NULL, &Buffer) != S_OK)
        return NULL;

    return Buffer;
}

INT main(VOID)
{
    HRESULT Result = S_OK;

    ID3D11Device* Device = NULL;
    ID3D11DeviceContext* Context = NULL;

    ID3D11Buffer* In = NULL;
    ID3D11Buffer* Out = NULL;

    BYTE Buffer[10] = "GPU stash";
    BYTE TryBuffer[10] = { 0 };

    Result = D3D11CreateDevice(NULL, D3D_DRIVER_TYPE_HARDWARE, NULL, 0, NULL, 0, D3D11_SDK_VERSION, &Device, NULL, &Context);
    if (!SUCCEEDED(Result))
        goto EXIT_ROUTINE;
    
    In = AllocateD3d11Memory(Device, ARRAY_SIZE(Buffer), FALSE);
    if (In == NULL)
        goto EXIT_ROUTINE;

    Out = AllocateD3d11Memory(Device, ARRAY_SIZE(Buffer), TRUE);
    if (Out == NULL)
        goto EXIT_ROUTINE;

    Id3d11MoveMemoryToGPUMemory(Context, In, Buffer);

    Id3d11PullMemoryFromGPUMemory(Context, In, Out, TryBuffer, ARRAY_SIZE(TryBuffer));

EXIT_ROUTINE:

    if (In)
        In->Release();

    if (Out)
        Out->Release();

    if (Device)
        Device->Release();

    if (Context)
        Context->Release();

    return ERROR_SUCCESS;
}
```

1.  Initializes Direct3D 11.
2.  Allocates GPU memory.
3.  Moves a sample buffer (`"GPU stash"`) to the GPU.
4.  Retrieves the buffer back into system memory.
5.  Releases all resources.

### **Practical Applications**

This technique can be applied to:

-   **Anti-forensics**: Hiding sensitive data from conventional RAM-based forensic tools.
-   **Malware Concealment**: Malware authors could use this to store payloads in VRAM.
-   **Security Research**: Exploring GPU-assisted cryptographic storage.

### **Limitations**

-   **VRAM Volatility**: Data in GPU memory is typically lost on system reboot.
-   **GPU Driver & API Restrictions**: Some GPUs might not support all Direct3D 11 buffer operations.
-   **Detection by Advanced Forensic Tools**: Newer forensic tools may scan GPU memory.

### **Future Enhancements**

-   Encrypting data before moving it into VRAM.
-   Using compute shaders for GPU-only decryption.
-   Investigating OpenCL or CUDA for cross-platform GPU storage.

### **Conclusion**

This approach demonstrates how GPU memory can be used for unconventional data storage. While it has valid security applications, it also raises concerns about its potential misuse. Future research into GPU-based security mechanisms could enhance both attack and defence strategies in modern computing.
