---
title: CUDA support — Apache Arrow v20.0.0
---

# CUDA support — Apache Arrow v20.0.0

url:: https://arrow.apache.org/docs/cpp/api/cuda.html
up: [[sources]]

#source

# CUDA support[#](#cuda-support "Permalink to this heading")

## Contexts[#](#contexts "Permalink to this heading")


class

CudaDeviceManager
[#](#_CPPv4N5arrow4cuda17CudaDeviceManagerE "Permalink to this definition")


:
    Public Functions


    [Result](https://arrow.apache.org/docs/cpp/api/support.html#_CPPv4I0EN5arrow6ResultE "arrow::Result")
    <
    std
    ::
    shared_ptr
    <
    [CudaDevice](#_CPPv4N5arrow4cuda10CudaDeviceE "arrow::cuda::CudaDevice")
    >
    >

    GetDevice
    (
    int

    device_number
    )
    [#](#_CPPv4N5arrow4cuda17CudaDeviceManager9GetDeviceEi "Permalink to this definition")


    :   Get a [CudaDevice](#classarrow_1_1cuda_1_1_cuda_device) instance for a particular device.


        Parameters
        :

        :   **device_number** – **[in]** the CUDA device number


    [Result](https://arrow.apache.org/docs/cpp/api/support.html#_CPPv4I0EN5arrow6ResultE "arrow::Result")
    <
    std
    ::
    shared_ptr
    <
    [CudaContext](#_CPPv4N5arrow4cuda11CudaContextE "arrow::cuda::CudaContext")
    >
    >

    GetContext
    (
    int

    device_number
    )
    [#](#_CPPv4N5arrow4cuda17CudaDeviceManager10GetContextEi "Permalink to this definition")


    :   Get the CUDA driver context for a particular device.


        Parameters
        :

        :   **device_number** – **[in]** the CUDA device number


        Returns
        :

        :   cached context


    [Result](https://arrow.apache.org/docs/cpp/api/support.html#_CPPv4I0EN5arrow6ResultE "arrow::Result")
    <
    std
    ::
    shared_ptr
    <
    [CudaContext](#_CPPv4N5arrow4cuda11CudaContextE "arrow::cuda::CudaContext")
    >
    >

    GetSharedContext
    (
    int

    device_number,
    void

    *
    handle
    )
    [#](#_CPPv4N5arrow4cuda17CudaDeviceManager16GetSharedContextEiPv "Permalink to this definition")


    :   Get the shared CUDA driver context for a particular device.


        Parameters
        :
        :
            * **device_number** – **[in]** the CUDA device number

            * **handle** – **[in]** CUDA context handle created by another library


        Returns
        :

        :   shared context


    [Result](https://arrow.apache.org/docs/cpp/api/support.html#_CPPv4I0EN5arrow6ResultE "arrow::Result")
    <
    std
    ::
    shared_ptr
    <
    [CudaHostBuffer](#_CPPv4N5arrow4cuda14CudaHostBufferE "arrow::cuda::CudaHostBuffer")
    >
    >

    AllocateHost
    (
    int

    device_number,
    int64_t

    nbytes
    )
    [#](#_CPPv4N5arrow4cuda17CudaDeviceManager12AllocateHostEi7int64_t "Permalink to this definition")


    :   Allocate host memory with fast access to given GPU device.


        Parameters
        :
        :
            * **device_number** – **[in]** the CUDA device number

            * **nbytes** – **[in]** number of bytes


        Returns
        :

        :   Host buffer or [Status](https://arrow.apache.org/docs/cpp/api/support.html#classarrow_1_1_status)


    [Status](https://arrow.apache.org/docs/cpp/api/support.html#_CPPv4N5arrow6StatusE "arrow::Status")

    FreeHost
    (
    void

    *
    data,
    int64_t

    nbytes
    )
    [#](#_CPPv4N5arrow4cuda17CudaDeviceManager8FreeHostEPv7int64_t "Permalink to this definition")


    :   Free host memory.

        The given memory pointer must have been allocated with AllocateHost.


class

CudaContext

:

public

std
::
enable_shared_from_this
<
[CudaContext](#_CPPv4N5arrow4cuda11CudaContextE "arrow::cuda::CudaContext")
>
[#](#_CPPv4N5arrow4cuda11CudaContextE "Permalink to this definition")


:   Object-oriented interface to the low-level CUDA driver API.

    Public Functions


    [Result](https://arrow.apache.org/docs/cpp/api/support.html#_CPPv4I0EN5arrow6ResultE "arrow::Result")
    <
    std
    ::
    unique_ptr
    <
    [CudaBuffer](#_CPPv4N5arrow4cuda10CudaBufferE "arrow::cuda::CudaBuffer")
    >
    >

    Allocate
    (
    int64_t

    nbytes
    )
    [#](#_CPPv4N5arrow4cuda11CudaContext8AllocateE7int64_t "Permalink to this definition")


    :   Allocate CUDA memory on GPU device for this context.


        Parameters
        :

        :   **nbytes** – **[in]** number of bytes


        Returns
        :

        :   the allocated buffer


    [Status](https://arrow.apache.org/docs/cpp/api/support.html#_CPPv4N5arrow6StatusE "arrow::Status")

    Free
    (
    void

    *
    device_ptr,
    int64_t

    nbytes
    )
    [#](#_CPPv4N5arrow4cuda11CudaContext4FreeEPv7int64_t "Permalink to this definition")


    :   Release CUDA memory on GPU device for this context.


        Parameters
        :
        :
            * **device_ptr** – **[in]** the buffer address

            * **nbytes** – **[in]** number of bytes


        Returns
        :

        :   [Status](https://arrow.apache.org/docs/cpp/api/support.html#classarrow_1_1_status)


    [Result](https://arrow.apache.org/docs/cpp/api/support.html#_CPPv4I0EN5arrow6ResultE "arrow::Result")
    <
    std
    ::
    shared_ptr
    <
    [CudaBuffer](#_CPPv4N5arrow4cuda10CudaBufferE "arrow::cuda::CudaBuffer")
    >
    >

    View
    (
    uint8_t

    *
    data,
    int64_t

    nbytes
    )
    [#](#_CPPv4N5arrow4cuda11CudaContext4ViewEP7uint8_t7int64_t "Permalink to this definition")


    :   Create a view of CUDA memory on GPU device of this context.

        Note

        The caller is responsible for allocating and freeing the memory as well as ensuring that the memory belongs to the CUDA context that this [CudaContext](#classarrow_1_1cuda_1_1_cuda_context) instance holds.


        Parameters
        :
        :
            * **data** – **[in]** the starting device address

            * **nbytes** – **[in]** number of bytes


        Returns
        :

        :   the view buffer


    [Result](https://arrow.apache.org/docs/cpp/api/support.html#_CPPv4I0EN5arrow6ResultE "arrow::Result")
    <
    std
    ::
    shared_ptr
    <
    [CudaBuffer](#_CPPv4N5arrow4cuda10CudaBufferE "arrow::cuda::CudaBuffer")
    >
    >

    OpenIpcBuffer
    (
    const

    [CudaIpcMemHandle](#_CPPv4N5arrow4cuda16CudaIpcMemHandleE "arrow::cuda::CudaIpcMemHandle")

    &
    ipc_handle
    )
    [#](#_CPPv4N5arrow4cuda11CudaContext13OpenIpcBufferERK16CudaIpcMemHandle "Permalink to this definition")


    :   Open existing CUDA IPC memory handle.


        Parameters
        :

        :   **ipc_handle** – **[in]** opaque pointer to CUipcMemHandle (driver API)


        Returns
        :

        :   a [CudaBuffer](#classarrow_1_1cuda_1_1_cuda_buffer) referencing the IPC segment


    [Status](https://arrow.apache.org/docs/cpp/api/support.html#_CPPv4N5arrow6StatusE "arrow::Status")

    CloseIpcBuffer
    (
    [CudaBuffer](#_CPPv4N5arrow4cuda10CudaBufferE "arrow::cuda::CudaBuffer")

    *
    buffer
    )
    [#](#_CPPv4N5arrow4cuda11CudaContext14CloseIpcBufferEP10CudaBuffer "Permalink to this definition")


    :   Close memory mapped with IPC buffer.


        Parameters
        :

        :   **buffer** – **[in]** a [CudaBuffer](#classarrow_1_1cuda_1_1_cuda_buffer) referencing


        Returns
        :

        :   [Status](https://arrow.apache.org/docs/cpp/api/support.html#classarrow_1_1_status)


    [Status](https://arrow.apache.org/docs/cpp/api/support.html#_CPPv4N5arrow6StatusE "arrow::Status")

    Synchronize
    (
    void
    )
    [#](#_CPPv4N5arrow4cuda11CudaContext11SynchronizeEv "Permalink to this definition")


    :   Block until the all device tasks are completed.


    void

    *
    handle
    (
    )

    const
    [#](#_CPPv4NK5arrow4cuda11CudaContext6handleEv "Permalink to this definition")


    :   Expose CUDA context handle to other libraries.


    std
    ::
    shared_ptr
    <
    [CudaMemoryManager](#_CPPv4N5arrow4cuda17CudaMemoryManagerE "arrow::cuda::CudaMemoryManager")
    >

    memory_manager
    (
    )

    const
    [#](#_CPPv4NK5arrow4cuda11CudaContext14memory_managerEv "Permalink to this definition")


    :   Return the default memory manager tied to this context’s device.


    std
    ::
    shared_ptr
    <
    [CudaDevice](#_CPPv4N5arrow4cuda10CudaDeviceE "arrow::cuda::CudaDevice")
    >

    device
    (
    )

    const
    [#](#_CPPv4NK5arrow4cuda11CudaContext6deviceEv "Permalink to this definition")


    :   Return the device instance associated with this context.


    int

    device_number
    (
    )

    const
    [#](#_CPPv4NK5arrow4cuda11CudaContext13device_numberEv "Permalink to this definition")


    :   Return the logical device number.


    [Result](https://arrow.apache.org/docs/cpp/api/support.html#_CPPv4I0EN5arrow6ResultE "arrow::Result")
    <
    uintptr_t
    >

    GetDeviceAddress
    (
    uint8_t

    *
    addr
    )
    [#](#_CPPv4N5arrow4cuda11CudaContext16GetDeviceAddressEP7uint8_t "Permalink to this definition")


    :   Return the device address that is reachable from kernels running in the context.

        The device address is defined as a memory address accessible by device. While it is often a device memory address, it can be also a host memory address, for instance, when the memory is allocated as host memory (using cudaMallocHost or cudaHostAlloc) or as managed memory (using cudaMallocManaged) or the host memory is page-locked (using cudaHostRegister).


        Parameters
        :

        :   **addr** – **[in]** device or host memory address


        Returns
        :

        :   the device address

## Devices[#](#devices "Permalink to this heading")


class

CudaDevice

:

public

arrow
::
[Device](https://arrow.apache.org/docs/cpp/api/memory.html#_CPPv4N5arrow6DeviceE "arrow::Device")
[#](#_CPPv4N5arrow4cuda10CudaDeviceE "Permalink to this definition")


:   [Device](https://arrow.apache.org/docs/cpp/api/memory.html#classarrow_1_1_device) implementation for CUDA.

    Each [CudaDevice](#classarrow_1_1cuda_1_1_cuda_device) instance is tied to a particular CUDA device (identified by its logical device number).  
    Public Functions


    virtual

    const

    char

    *
    type_name
    (
    )

    const

    override
    [#](#_CPPv4NK5arrow4cuda10CudaDevice9type_nameEv "Permalink to this definition")


    :   A shorthand for this device’s type.

        The returned value is different for each device class, but is the same for all instances of a given class. It can be used as a replacement for RTTI.


    virtual

    std
    ::
    string

    ToString
    (
    )

    const

    override
    [#](#_CPPv4NK5arrow4cuda10CudaDevice8ToStringEv "Permalink to this definition")


    :   A human-readable description of the device.

        The returned value should be detailed enough to distinguish between different instances, where necessary.


    virtual

    bool

    Equals
    (
    const

    [Device](https://arrow.apache.org/docs/cpp/api/memory.html#_CPPv4N5arrow6DeviceE "arrow::Device")
    &
    )

    const

    override
    [#](#_CPPv4NK5arrow4cuda10CudaDevice6EqualsERK6Device "Permalink to this definition")


    :   Whether this instance points to the same device as another one.


    virtual

    std
    ::
    shared_ptr
    <
    [MemoryManager](https://arrow.apache.org/docs/cpp/api/memory.html#_CPPv4N5arrow13MemoryManagerE "arrow::MemoryManager")
    >

    default_memory_manager
    (
    )

    override
    [#](#_CPPv4N5arrow4cuda10CudaDevice22default_memory_managerEv "Permalink to this definition")


    :   Return a [MemoryManager](https://arrow.apache.org/docs/cpp/api/memory.html#classarrow_1_1_memory_manager) instance tied to this device.

        The returned instance uses default parameters for this device type’s [MemoryManager](https://arrow.apache.org/docs/cpp/api/memory.html#classarrow_1_1_memory_manager) implementation. Some devices also allow constructing [MemoryManager](https://arrow.apache.org/docs/cpp/api/memory.html#classarrow_1_1_memory_manager) instances with non-default parameters.


    inline

    virtual

    DeviceAllocationType

    device_type
    (
    )

    const

    override
    [#](#_CPPv4NK5arrow4cuda10CudaDevice11device_typeEv "Permalink to this definition")


    :   Return the DeviceAllocationType of this device.


    inline

    virtual

    int64_t

    device_id
    (
    )

    const

    override
    [#](#_CPPv4NK5arrow4cuda10CudaDevice9device_idEv "Permalink to this definition")


    :   A device ID to identify this device if there are multiple of this type.

        If there is no “device_id” equivalent (such as for the main CPU device on non-numa systems) returns -1.


    int

    device_number
    (
    )

    const
    [#](#_CPPv4NK5arrow4cuda10CudaDevice13device_numberEv "Permalink to this definition")


    :   Return the device logical number.


    std
    ::
    string

    device_name
    (
    )

    const
    [#](#_CPPv4NK5arrow4cuda10CudaDevice11device_nameEv "Permalink to this definition")


    :   Return the GPU model name.


    int64_t

    total_memory
    (
    )

    const
    [#](#_CPPv4NK5arrow4cuda10CudaDevice12total_memoryEv "Permalink to this definition")


    :   Return total memory on this device.


    int

    handle
    (
    )

    const
    [#](#_CPPv4NK5arrow4cuda10CudaDevice6handleEv "Permalink to this definition")


    :   Return a raw CUDA device handle.

        The returned value can be used to expose this device to other libraries. It should be interpreted as CUdevice.


    [Result](https://arrow.apache.org/docs/cpp/api/support.html#_CPPv4I0EN5arrow6ResultE "arrow::Result")
    <
    std
    ::
    shared_ptr
    <
    [CudaContext](#_CPPv4N5arrow4cuda11CudaContextE "arrow::cuda::CudaContext")
    >
    >

    GetContext
    (
    )
    [#](#_CPPv4N5arrow4cuda10CudaDevice10GetContextEv "Permalink to this definition")


    :   Get a CUDA driver context for this device.

        The returned context is associated with the primary CUDA context for the device. This is the recommended way of getting a context for a device, as it allows interoperating transparently with any library using the primary CUDA context API.


    [Result](https://arrow.apache.org/docs/cpp/api/support.html#_CPPv4I0EN5arrow6ResultE "arrow::Result")
    <
    std
    ::
    shared_ptr
    <
    [CudaContext](#_CPPv4N5arrow4cuda11CudaContextE "arrow::cuda::CudaContext")
    >
    >

    GetSharedContext
    (
    void

    *
    handle
    )
    [#](#_CPPv4N5arrow4cuda10CudaDevice16GetSharedContextEPv "Permalink to this definition")


    :   Get a CUDA driver context for this device, using an existing handle.

        The handle is not owned: it will not be released when the [CudaContext](#classarrow_1_1cuda_1_1_cuda_context) is destroyed. This function should only be used if you need interoperation with a library that uses a non-primary context.


        Parameters
        :

        :   **handle** – **[in]** CUDA context handle created by another library


    [Result](https://arrow.apache.org/docs/cpp/api/support.html#_CPPv4I0EN5arrow6ResultE "arrow::Result")
    <
    std
    ::
    shared_ptr
    <
    [CudaHostBuffer](#_CPPv4N5arrow4cuda14CudaHostBufferE "arrow::cuda::CudaHostBuffer")
    >
    >

    AllocateHostBuffer
    (
    int64_t

    size
    )
    [#](#_CPPv4N5arrow4cuda10CudaDevice18AllocateHostBufferE7int64_t "Permalink to this definition")


    :   Allocate a host-residing, GPU-accessible buffer.

        The buffer is allocated using this device’s primary context.


        Parameters
        :

        :   **size** – **[in]** The buffer size in bytes


    virtual

    [Result](https://arrow.apache.org/docs/cpp/api/support.html#_CPPv4I0EN5arrow6ResultE "arrow::Result")
    <
    std
    ::
    shared_ptr
    <
    [Device](https://arrow.apache.org/docs/cpp/api/memory.html#_CPPv4N5arrow6DeviceE "arrow::Device")
    ::
    [Stream](https://arrow.apache.org/docs/cpp/api/memory.html#_CPPv4N5arrow6Device6StreamE "arrow::Device::Stream")
    >
    >

    MakeStream
    (
    unsigned

    int

    flags
    )

    override
    [#](#_CPPv4N5arrow4cuda10CudaDevice10MakeStreamEj "Permalink to this definition")


    :   Create a CUstream wrapper in the current context.


    virtual

    [Result](https://arrow.apache.org/docs/cpp/api/support.html#_CPPv4I0EN5arrow6ResultE "arrow::Result")
    <
    std
    ::
    shared_ptr
    <
    [Device](https://arrow.apache.org/docs/cpp/api/memory.html#_CPPv4N5arrow6DeviceE "arrow::Device")
    ::
    [Stream](https://arrow.apache.org/docs/cpp/api/memory.html#_CPPv4N5arrow6Device6StreamE "arrow::Device::Stream")
    >
    >

    WrapStream
    (
    void

    *
    device_stream,
    [Stream](#_CPPv4N5arrow4cuda10CudaDevice6StreamE "arrow::cuda::CudaDevice::Stream")
    ::
    release_fn_t

    release_fn
    )

    override
    [#](#_CPPv4N5arrow4cuda10CudaDevice10WrapStreamEPvN6Stream12release_fn_tE "Permalink to this definition")


    :   Wrap a pointer to an existing stream.


        Parameters
        :
        :
            * **device_stream** – passed in stream (should be a CUstream*)

            * **release_fn** – destructor to free the stream. nullptr may be passed to indicate there is no destruction/freeing necessary.


    Public Static Functions


    static

    [Result](https://arrow.apache.org/docs/cpp/api/support.html#_CPPv4I0EN5arrow6ResultE "arrow::Result")
    <
    std
    ::
    shared_ptr
    <
    [CudaDevice](#_CPPv4N5arrow4cuda10CudaDeviceE "arrow::cuda::CudaDevice")
    >
    >

    Make
    (
    int

    device_number
    )
    [#](#_CPPv4N5arrow4cuda10CudaDevice4MakeEi "Permalink to this definition")


    :   Return a [CudaDevice](#classarrow_1_1cuda_1_1_cuda_device) instance for a particular device.


        Parameters
        :

        :   **device_number** – **[in]** the CUDA device number


    class

    Stream

    :

    public

    arrow
    ::
    [Device](https://arrow.apache.org/docs/cpp/api/memory.html#_CPPv4N5arrow6DeviceE "arrow::Device")
    ::
    [Stream](https://arrow.apache.org/docs/cpp/api/memory.html#_CPPv4N5arrow6Device6StreamE "arrow::Device::Stream")
    [#](#_CPPv4N5arrow4cuda10CudaDevice6StreamE "Permalink to this definition")


    :   EXPERIMENTAL: Wrapper for CUstreams.

        Does not *own* the CUstream object which must be separately constructed and freed using cuStreamCreate and cuStreamDestroy (or equivalent). Default construction will use the cuda default stream, and does not allow construction from literal 0 or nullptr.  
        Public Functions


        virtual

        [Status](https://arrow.apache.org/docs/cpp/api/support.html#_CPPv4N5arrow6StatusE "arrow::Status")

        WaitEvent
        (
        const

        [Device](https://arrow.apache.org/docs/cpp/api/memory.html#_CPPv4N5arrow6DeviceE "arrow::Device")
        ::
        [SyncEvent](https://arrow.apache.org/docs/cpp/api/memory.html#_CPPv4N5arrow6Device9SyncEventE "arrow::Device::SyncEvent")
        &
        )

        override
        [#](#_CPPv4N5arrow4cuda10CudaDevice6Stream9WaitEventERKN6Device9SyncEventE "Permalink to this definition")


        :   Make the stream wait on the provided event.

            Tells the stream that it should wait until the synchronization event is completed without blocking the CPU.


        virtual

        [Status](https://arrow.apache.org/docs/cpp/api/support.html#_CPPv4N5arrow6StatusE "arrow::Status")

        Synchronize
        (
        )

        const

        override
        [#](#_CPPv4NK5arrow4cuda10CudaDevice6Stream11SynchronizeEv "Permalink to this definition")


        :   Blocks the current thread until a stream’s remaining tasks are completed.


    class

    SyncEvent

    :

    public

    arrow
    ::
    [Device](https://arrow.apache.org/docs/cpp/api/memory.html#_CPPv4N5arrow6DeviceE "arrow::Device")
    ::
    [SyncEvent](https://arrow.apache.org/docs/cpp/api/memory.html#_CPPv4N5arrow6Device9SyncEventE "arrow::Device::SyncEvent")
    [#](#_CPPv4N5arrow4cuda10CudaDevice9SyncEventE "Permalink to this definition")


    :
        Public Functions


        virtual

        [Status](https://arrow.apache.org/docs/cpp/api/support.html#_CPPv4N5arrow6StatusE "arrow::Status")

        Wait
        (
        )

        override
        [#](#_CPPv4N5arrow4cuda10CudaDevice9SyncEvent4WaitEv "Permalink to this definition")


        :   Block until the sync event is marked completed.


        virtual

        [Status](https://arrow.apache.org/docs/cpp/api/support.html#_CPPv4N5arrow6StatusE "arrow::Status")

        Record
        (
        const

        [Device](https://arrow.apache.org/docs/cpp/api/memory.html#_CPPv4N5arrow6DeviceE "arrow::Device")
        ::
        [Stream](https://arrow.apache.org/docs/cpp/api/memory.html#_CPPv4N5arrow6Device6StreamE "arrow::Device::Stream")
        &
        )

        override
        [#](#_CPPv4N5arrow4cuda10CudaDevice9SyncEvent6RecordERKN6Device6StreamE "Permalink to this definition")


        :   Record the wrapped event on the stream.

            Once the stream completes the tasks previously added to it, it will trigger the event.


class

CudaMemoryManager

:

public

arrow
::
[MemoryManager](https://arrow.apache.org/docs/cpp/api/memory.html#_CPPv4N5arrow13MemoryManagerE "arrow::MemoryManager")
[#](#_CPPv4N5arrow4cuda17CudaMemoryManagerE "Permalink to this definition")


:   [MemoryManager](https://arrow.apache.org/docs/cpp/api/memory.html#classarrow_1_1_memory_manager) implementation for CUDA.

    Public Functions

    :   Create a RandomAccessFile to read a particular buffer.

        The given buffer must be tied to this [MemoryManager](https://arrow.apache.org/docs/cpp/api/memory.html#classarrow_1_1_memory_manager).

        See also the [Buffer::GetReader](https://arrow.apache.org/docs/cpp/api/memory.html#classarrow_1_1_buffer_1aeacfa7e7ff56af24b523cff17775698b) shorthand.

    :   Create a OutputStream to write to a particular buffer.

        The given buffer must be mutable and tied to this [MemoryManager](https://arrow.apache.org/docs/cpp/api/memory.html#classarrow_1_1_memory_manager). The returned stream object writes into the buffer’s underlying memory (but it won’t resize it).

        See also the [Buffer::GetWriter](https://arrow.apache.org/docs/cpp/api/memory.html#classarrow_1_1_buffer_1ad3aff6f979cd6ebf0497fba6108c8f7f) shorthand.


    virtual

    [Result](https://arrow.apache.org/docs/cpp/api/support.html#_CPPv4I0EN5arrow6ResultE "arrow::Result")
    <
    std
    ::
    unique_ptr
    <
    [Buffer](https://arrow.apache.org/docs/cpp/api/memory.html#_CPPv4N5arrow6BufferE "arrow::Buffer")
    >
    >

    AllocateBuffer
    (
    int64_t

    size
    )

    override
    [#](#_CPPv4N5arrow4cuda17CudaMemoryManager14AllocateBufferE7int64_t "Permalink to this definition")


    :   Allocate a (mutable) [Buffer](https://arrow.apache.org/docs/cpp/api/memory.html#classarrow_1_1_buffer).

        The buffer will be allocated in the device’s memory.


    std
    ::
    shared_ptr
    <
    [CudaDevice](#_CPPv4N5arrow4cuda10CudaDeviceE "arrow::cuda::CudaDevice")
    >

    cuda_device
    (
    )

    const
    [#](#_CPPv4NK5arrow4cuda17CudaMemoryManager11cuda_deviceEv "Permalink to this definition")


    :   The [CudaDevice](#classarrow_1_1cuda_1_1_cuda_device) instance tied to this [MemoryManager](https://arrow.apache.org/docs/cpp/api/memory.html#classarrow_1_1_memory_manager).

        This is a useful shorthand returning a concrete-typed pointer, avoiding having to cast the [device()](https://arrow.apache.org/docs/cpp/api/memory.html#classarrow_1_1_memory_manager_1a0bd3ad591750cd15277cf569eb6e985e) result.


    virtual

    [Result](https://arrow.apache.org/docs/cpp/api/support.html#_CPPv4I0EN5arrow6ResultE "arrow::Result")
    <
    std
    ::
    shared_ptr
    <
    [Device](https://arrow.apache.org/docs/cpp/api/memory.html#_CPPv4N5arrow6DeviceE "arrow::Device")
    ::
    [SyncEvent](https://arrow.apache.org/docs/cpp/api/memory.html#_CPPv4N5arrow6Device9SyncEventE "arrow::Device::SyncEvent")
    >
    >

    MakeDeviceSyncEvent
    (
    )

    override
    [#](#_CPPv4N5arrow4cuda17CudaMemoryManager19MakeDeviceSyncEventEv "Permalink to this definition")


    :   Creates a wrapped CUevent.

        Will call cuEventCreate and it will call cuEventDestroy internally when the event is destructed.


    virtual

    [Result](https://arrow.apache.org/docs/cpp/api/support.html#_CPPv4I0EN5arrow6ResultE "arrow::Result")
    <
    std
    ::
    shared_ptr
    <
    [Device](https://arrow.apache.org/docs/cpp/api/memory.html#_CPPv4N5arrow6DeviceE "arrow::Device")
    ::
    [SyncEvent](https://arrow.apache.org/docs/cpp/api/memory.html#_CPPv4N5arrow6Device9SyncEventE "arrow::Device::SyncEvent")
    >
    >

    WrapDeviceSyncEvent
    (
    void

    *
    sync_event,
    [Device](https://arrow.apache.org/docs/cpp/api/memory.html#_CPPv4N5arrow6DeviceE "arrow::Device")
    ::
    [SyncEvent](https://arrow.apache.org/docs/cpp/api/memory.html#_CPPv4N5arrow6Device9SyncEventE "arrow::Device::SyncEvent")
    ::
    release_fn_t

    release_sync_event
    )

    override
    [#](#_CPPv4N5arrow4cuda17CudaMemoryManager19WrapDeviceSyncEventEPvN6Device9SyncEvent12release_fn_tE "Permalink to this definition")


    :   Wraps an existing event into a sync event.


        Parameters
        :
        :
            * **sync_event** – the event to wrap, must be a CUevent*

            * **release_sync_event** – a function to call during destruction, nullptr or a no-op function can be passed to indicate ownership is maintained externally


## Buffers[#](#buffers "Permalink to this heading")


class

CudaBuffer

:

public

arrow
::
[Buffer](https://arrow.apache.org/docs/cpp/api/memory.html#_CPPv4N5arrow6BufferE "arrow::Buffer")
[#](#_CPPv4N5arrow4cuda10CudaBufferE "Permalink to this definition")


:   An Arrow buffer located on a GPU device.

    Be careful using this in any Arrow code which may not be GPU-aware  
    Public Functions


    [Status](https://arrow.apache.org/docs/cpp/api/support.html#_CPPv4N5arrow6StatusE "arrow::Status")

    CopyToHost
    (
    const

    int64_t

    position,
    const

    int64_t

    nbytes,
    void

    *
    out
    )

    const
    [#](#_CPPv4NK5arrow4cuda10CudaBuffer10CopyToHostEK7int64_tK7int64_tPv "Permalink to this definition")


    :   Copy memory from GPU device to CPU host.


        Parameters
        :
        :
            * **position** – **[in]** start position inside buffer to copy bytes from

            * **nbytes** – **[in]** number of bytes to copy

            * **out** – **[out]** start address of the host memory area to copy to


        Returns
        :

        :   [Status](https://arrow.apache.org/docs/cpp/api/support.html#classarrow_1_1_status)


    [Status](https://arrow.apache.org/docs/cpp/api/support.html#_CPPv4N5arrow6StatusE "arrow::Status")

    CopyFromHost
    (
    const

    int64_t

    position,
    const

    void

    *
    data,
    int64_t

    nbytes
    )
    [#](#_CPPv4N5arrow4cuda10CudaBuffer12CopyFromHostEK7int64_tPKv7int64_t "Permalink to this definition")


    :   Copy memory to device at position.


        Parameters
        :
        :
            * **position** – **[in]** start position to copy bytes to

            * **data** – **[in]** the host data to copy

            * **nbytes** – **[in]** number of bytes to copy


        Returns
        :

        :   [Status](https://arrow.apache.org/docs/cpp/api/support.html#classarrow_1_1_status)


    [Status](https://arrow.apache.org/docs/cpp/api/support.html#_CPPv4N5arrow6StatusE "arrow::Status")

    CopyFromDevice
    (
    const

    int64_t

    position,
    const

    void

    *
    data,
    int64_t

    nbytes
    )
    [#](#_CPPv4N5arrow4cuda10CudaBuffer14CopyFromDeviceEK7int64_tPKv7int64_t "Permalink to this definition")


    :   Copy memory from device to device at position.

        Note

        It is assumed that both source and destination device memories have been allocated within the same context.


        Parameters
        :
        :
            * **position** – **[in]** start position inside buffer to copy bytes to

            * **data** – **[in]** start address of the device memory area to copy from

            * **nbytes** – **[in]** number of bytes to copy


        Returns
        :

        :   [Status](https://arrow.apache.org/docs/cpp/api/support.html#classarrow_1_1_status)

    :   Copy memory from another device to device at position.


        Parameters
        :
        :
            * **src_ctx** – **[in]** context of the source device memory

            * **position** – **[in]** start position inside buffer to copy bytes to

            * **data** – **[in]** start address of the another device memory area to copy from

            * **nbytes** – **[in]** number of bytes to copy


        Returns
        :

        :   [Status](https://arrow.apache.org/docs/cpp/api/support.html#classarrow_1_1_status)


    virtual

    [Result](https://arrow.apache.org/docs/cpp/api/support.html#_CPPv4I0EN5arrow6ResultE "arrow::Result")
    <
    std
    ::
    shared_ptr
    <
    [CudaIpcMemHandle](#_CPPv4N5arrow4cuda16CudaIpcMemHandleE "arrow::cuda::CudaIpcMemHandle")
    >
    >

    ExportForIpc
    (
    )
    [#](#_CPPv4N5arrow4cuda10CudaBuffer12ExportForIpcEv "Permalink to this definition")


    :   Expose this device buffer as IPC memory which can be used in other processes.

        Note

        After calling this function, this device memory will not be freed when the [CudaBuffer](#classarrow_1_1cuda_1_1_cuda_buffer) is destructed


        Returns
        :

        :   Handle or [Status](https://arrow.apache.org/docs/cpp/api/support.html#classarrow_1_1_status)

    Public Static Functions

    :   Convert back generic buffer into [CudaBuffer](#classarrow_1_1cuda_1_1_cuda_buffer).

        Note

        This function returns an error if the buffer isn’t backed by GPU memory


        Parameters
        :

        :   **buffer** – **[in]** buffer to convert


        Returns
        :

        :   [CudaBuffer](#classarrow_1_1cuda_1_1_cuda_buffer) or [Status](https://arrow.apache.org/docs/cpp/api/support.html#classarrow_1_1_status)


class

CudaHostBuffer

:

public

arrow
::
[MutableBuffer](https://arrow.apache.org/docs/cpp/api/memory.html#_CPPv4N5arrow13MutableBufferE "arrow::MutableBuffer")
[#](#_CPPv4N5arrow4cuda14CudaHostBufferE "Permalink to this definition")


:   Device-accessible CPU memory created using cudaHostAlloc.

    Public Functions

    :   Return a device address the GPU can read this memory from.

## Memory Input / Output[#](#memory-input-output "Permalink to this heading")


class

CudaBufferReader

:

public

arrow
::
io
::
internal
::
RandomAccessFileConcurrencyWrapper
<
[CudaBufferReader](#_CPPv4N5arrow4cuda16CudaBufferReaderE "arrow::cuda::CudaBufferReader")
>
[#](#_CPPv4N5arrow4cuda16CudaBufferReaderE "Permalink to this definition")


:   File interface for zero-copy read from CUDA buffers.

    CAUTION: reading to a [Buffer](https://arrow.apache.org/docs/cpp/api/memory.html#classarrow_1_1_buffer) returns a [Buffer](https://arrow.apache.org/docs/cpp/api/memory.html#classarrow_1_1_buffer) pointing to device memory. It will generally not be compatible with Arrow code expecting a buffer pointing to CPU memory. Reading to a raw pointer, though, copies device memory into the host memory pointed to.  
    Public Functions


    virtual

    bool

    closed
    (
    )

    const

    override
    [#](#_CPPv4NK5arrow4cuda16CudaBufferReader6closedEv "Permalink to this definition")


    :   Return whether the stream is closed.


    virtual

    bool

    supports_zero_copy
    (
    )

    const

    override
    [#](#_CPPv4NK5arrow4cuda16CudaBufferReader18supports_zero_copyEv "Permalink to this definition")


    :   Return true if InputStream is capable of zero copy [Buffer](https://arrow.apache.org/docs/cpp/api/memory.html#classarrow_1_1_buffer) reads.

        Zero copy reads imply the use of Buffer-returning [Read()](https://arrow.apache.org/docs/cpp/api/io.html#classarrow_1_1io_1_1_readable_1a1ffee8b3491907c625faff85274eee54) overloads.


class

CudaBufferWriter

:

public

arrow
::
io
::
WritableFile
[#](#_CPPv4N5arrow4cuda16CudaBufferWriterE "Permalink to this definition")


:   File interface for writing to CUDA buffers, with optional buffering.

    Public Functions


    virtual

    [Status](https://arrow.apache.org/docs/cpp/api/support.html#_CPPv4N5arrow6StatusE "arrow::Status")

    Close
    (
    )

    override
    [#](#_CPPv4N5arrow4cuda16CudaBufferWriter5CloseEv "Permalink to this definition")


    :   Close writer and flush buffered bytes to GPU.


    virtual

    bool

    closed
    (
    )

    const

    override
    [#](#_CPPv4NK5arrow4cuda16CudaBufferWriter6closedEv "Permalink to this definition")


    :   Return whether the stream is closed.


    virtual

    [Status](https://arrow.apache.org/docs/cpp/api/support.html#_CPPv4N5arrow6StatusE "arrow::Status")

    Flush
    (
    )

    override
    [#](#_CPPv4N5arrow4cuda16CudaBufferWriter5FlushEv "Permalink to this definition")


    :   Flush buffered bytes to GPU.


    virtual

    [Status](https://arrow.apache.org/docs/cpp/api/support.html#_CPPv4N5arrow6StatusE "arrow::Status")

    Write
    (
    const

    void

    *
    data,
    int64_t

    nbytes
    )

    override
    [#](#_CPPv4N5arrow4cuda16CudaBufferWriter5WriteEPKv7int64_t "Permalink to this definition")


    :   Write the given data to the stream.

        This method always processes the bytes in full. Depending on the semantics of the stream, the data may be written out immediately, held in a buffer, or written asynchronously. In the case where the stream buffers the data, it will be copied. To avoid potentially large copies, use the Write variant that takes an owned [Buffer](https://arrow.apache.org/docs/cpp/api/memory.html#classarrow_1_1_buffer).


    virtual

    [Result](https://arrow.apache.org/docs/cpp/api/support.html#_CPPv4I0EN5arrow6ResultE "arrow::Result")
    <
    int64_t
    >

    Tell
    (
    )

    const

    override
    [#](#_CPPv4NK5arrow4cuda16CudaBufferWriter4TellEv "Permalink to this definition")


    :   Return the position in this stream.


    [Status](https://arrow.apache.org/docs/cpp/api/support.html#_CPPv4N5arrow6StatusE "arrow::Status")

    SetBufferSize
    (
    const

    int64_t

    buffer_size
    )
    [#](#_CPPv4N5arrow4cuda16CudaBufferWriter13SetBufferSizeEK7int64_t "Permalink to this definition")


    :   Set CPU buffer size to limit calls to cudaMemcpy.

        By default writes are unbuffered


        Parameters
        :

        :   **buffer_size** – **[in]** the size of CPU buffer to allocate


        Returns
        :

        :   [Status](https://arrow.apache.org/docs/cpp/api/support.html#classarrow_1_1_status)


    int64_t

    buffer_size
    (
    )

    const
    [#](#_CPPv4NK5arrow4cuda16CudaBufferWriter11buffer_sizeEv "Permalink to this definition")


    :   Returns size of host (CPU) buffer, 0 for unbuffered.


    int64_t

    num_bytes_buffered
    (
    )

    const
    [#](#_CPPv4NK5arrow4cuda16CudaBufferWriter18num_bytes_bufferedEv "Permalink to this definition")


    :   Returns number of bytes buffered on host.

## IPC[#](#ipc "Permalink to this heading")


class

CudaIpcMemHandle
[#](#_CPPv4N5arrow4cuda16CudaIpcMemHandleE "Permalink to this definition")


:
    Public Static Functions


    static

    [Result](https://arrow.apache.org/docs/cpp/api/support.html#_CPPv4I0EN5arrow6ResultE "arrow::Result")
    <
    std
    ::
    shared_ptr
    <
    [CudaIpcMemHandle](#_CPPv4N5arrow4cuda16CudaIpcMemHandleE "arrow::cuda::CudaIpcMemHandle")
    >
    >

    FromBuffer
    (
    const

    void

    *
    opaque_handle
    )
    [#](#_CPPv4N5arrow4cuda16CudaIpcMemHandle10FromBufferEPKv "Permalink to this definition")


    :   Create [CudaIpcMemHandle](#classarrow_1_1cuda_1_1_cuda_ipc_mem_handle) from opaque buffer (e.g.

        from another process)


        Parameters
        :

        :   **opaque_handle** – **[in]** a CUipcMemHandle as a const void*


        Returns
        :

        :   Handle or [Status](https://arrow.apache.org/docs/cpp/api/support.html#classarrow_1_1_status)


Result
<
std
::
shared_ptr
<
CudaBuffer
>
>

SerializeRecordBatch
(
const

RecordBatch

&
batch,
CudaContext

*
ctx
)
[#](#_CPPv420SerializeRecordBatchRK11RecordBatchP11CudaContext "Permalink to this definition")


:   Write record batch message to GPU device memory.


    Parameters
    :
    :
        * **batch** – **[in]** record batch to write

        * **ctx** – **[in]** [CudaContext](#classarrow_1_1cuda_1_1_cuda_context) to allocate device memory from


    Returns
    :

    :   [CudaBuffer](#classarrow_1_1cuda_1_1_cuda_buffer) or [Status](https://arrow.apache.org/docs/cpp/api/support.html#classarrow_1_1_status)

:   ReadRecordBatch specialized to handle metadata on CUDA device.


    Parameters
    :
    :
        * **schema** – **[in]** the [Schema](https://arrow.apache.org/docs/cpp/api/datatype.html#classarrow_1_1_schema) for the record batch

        * **dictionary_memo** – **[in]** DictionaryMemo which has any dictionaries. Can be nullptr if you are sure there are no dictionary-encoded fields

        * **buffer** – **[in]** a [CudaBuffer](#classarrow_1_1cuda_1_1_cuda_buffer) containing the complete IPC message

        * **pool** – **[in]** a [MemoryPool](https://arrow.apache.org/docs/cpp/api/memory.html#classarrow_1_1_memory_pool) to use for allocating space for the metadata


    Returns
    :

    :   [RecordBatch](https://arrow.apache.org/docs/cpp/api/table.html#classarrow_1_1_record_batch) or [Status](https://arrow.apache.org/docs/cpp/api/support.html#classarrow_1_1_status)

