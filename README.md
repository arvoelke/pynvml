Python bindings to the NVIDIA Management Library
================================================

Provides a Python interface to GPU management and monitoring functions.

This is a wrapper around the NVML library.
For information about the NVML library, see the NVML developer page
http://developer.nvidia.com/nvidia-management-library-nvml

Note this file can be run with 'python -m doctest -v README.txt'
although the results are system dependent

Requires
--------
Python 3, or an earlier version with the ctypes module.

Installation
------------

    pip install .

Usage
-----

You can use the lower level nvml bindings

```python
>>> from pynvml import *
>>> nvmlInit()
>>> print "Driver Version:", nvmlSystemGetDriverVersion()
Driver Version: 410.00
>>> deviceCount = nvmlDeviceGetCount()
>>> for i in range(deviceCount):
...     handle = nvmlDeviceGetHandleByIndex(i)
...     print "Device", i, ":", nvmlDeviceGetName(handle)
...
Device 0 : Tesla V100

>>> nvmlShutdown()
```

Or the higher level nvidia_smi API

```python
from pynvml.smi import nvidia_smi
nvsmi = nvidia_smi.getInstance()
nvsmi.DeviceQuery('memory.free, memory.total')
```

Functions
---------
Python methods wrap NVML functions, implemented in a C shared library.
Each function's use is the same with the following exceptions:

- Instead of returning error codes, failing error codes are raised as
  Python exceptions.

    ```python
    >>> try:
    ...     nvmlDeviceGetCount()
    ... except NVMLError as error:
    ...     print(error)
    ...
    Uninitialized
    ```

- C function output parameters are returned from the corresponding
  Python function left to right.

    ```c
    nvmlReturn_t nvmlDeviceGetEccMode(nvmlDevice_t device,
                                      nvmlEnableState_t *current,
                                      nvmlEnableState_t *pending);
    ```

    ```python
    >>> nvmlInit()
    >>> handle = nvmlDeviceGetHandleByIndex(0)
    >>> (current, pending) = nvmlDeviceGetEccMode(handle)
    ```

- C structs are converted into Python classes.

    ```c
    nvmlReturn_t DECLDIR nvmlDeviceGetMemoryInfo(nvmlDevice_t device,
                                                 nvmlMemory_t *memory);
    typedef struct nvmlMemory_st {
        unsigned long long total;
        unsigned long long free;
        unsigned long long used;
    } nvmlMemory_t;
    ```

    ```python
    >>> info = nvmlDeviceGetMemoryInfo(handle)
    >>> print "Total memory:", info.total
    Total memory: 5636292608
    >>> print "Free memory:", info.free
    Free memory: 5578420224
    >>> print "Used memory:", info.used
    Used memory: 57872384
    ```

- Python handles string buffer creation.

    ```c
    nvmlReturn_t nvmlSystemGetDriverVersion(char* version,
                                            unsigned int length);
    ```

    ```python
    >>> version = nvmlSystemGetDriverVersion();
    >>> nvmlShutdown()
    ```

For usage information see the NVML documentation.

Variables
---------

All meaningful NVML constants and enums are exposed in Python.

The NVML_VALUE_NOT_AVAILABLE constant is not used.  Instead None is mapped to the field.

Release Notes
-------------

-   Version 2.285.0
    - Added new functions for NVML 2.285.  See NVML documentation for more information.
    - Ported to support Python 3.0 and Python 2.0 syntax.
    - Added nvidia_smi.py tool as a sample app.
-   Version 3.295.0
    - Added new functions for NVML 3.295.  See NVML documentation for more information.
    - Updated nvidia_smi.py tool
      - Includes additional error handling
-   Version 4.304.0
    - Added new functions for NVML 4.304.  See NVML documentation for more information.
    - Updated nvidia_smi.py tool
-   Version 4.304.3
    - Fixing nvmlUnitGetDeviceCount bug
-   Version 5.319.0
    - Added new functions for NVML 5.319.  See NVML documentation for more information.
-   Version 6.340.0
    - Added new functions for NVML 6.340.  See NVML documentation for more information.
-   Version 7.346.0
    - Added new functions for NVML 7.346.  See NVML documentation for more information.
-   Version 7.352.0
    - Added new functions for NVML 7.352.  See NVML documentation for more information.
-   Version 8.0.0
    - Refactor code to a nvidia_smi singleton class
    - Added DeviceQuery that returns a dictionary of (name, value).
    - Added filter parameters on DeviceQuery to match query api in nvidia-smi
    - Added filter parameters on XmlDeviceQuery to match query api in nvidia-smi
    - Added integer enumeration for filter strings to reduce overhead for performance monitoring.
    - Added loop(filter) method with async and callback support
-   Version 8.0.1
    - Restructuring directories into two packages (pynvml and nvidia_smi)
    - Adding initial tests for both packages
    - Some name-convention cleanup in pynvml
-   Version 8.0.2
    - Added NVLink function wrappers for pynvml module
-   Version 8.0.3
    - Added versioneer
    - Fixed nvmlDeviceGetNvLinkUtilizationCounter bug


Copyright
---------
Copyright (c) 2011-2019, NVIDIA Corporation.  All rights reserved.

License
-------
Redistribution and use in source and binary forms, with or without modification, are permitted provided that the following conditions are met:

- Redistributions of source code must retain the above copyright notice, this list of conditions and the following disclaimer.

- Redistributions in binary form must reproduce the above copyright notice, this list of conditions and the following disclaimer in the documentation and/or other materials provided with the distribution.

- Neither the name of the NVIDIA Corporation nor the names of its contributors may be used to endorse or promote products derived from this software without specific prior written permission.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
