# Remote functions

The function decorator is the simplest way to use KCoral. Add
`@client.function()` to a Python function, then call `.remote()` to run it on
the server and receive its return value. KCoral builds and submits the program
for you.

With the [client installed](../getting-started/installation.md), save this
example in a Python file and replace the URL with your GPU server's address:

```python
from kcoral import Client

with Client("http://127.0.0.1:8000") as client:

    @client.function(timeout=30)
    def gpu_sum(n):
        import torch

        return torch.arange(n, device="cuda").sum().item()

    print(gpu_sum.remote(4))  # 6
```

The server creates the values 0 through 3 on its GPU and returns their sum.
`timeout=30` sets the server execution limit in seconds.

- Define the function in a Python file so KCoral can read its source.
- Import dependencies inside the function and install them on the server.
- Pass inputs as arguments; surrounding variables and external globals are
  not captured. Keep the client open while making remote calls.
- `.remote()` runs on the server; an ordinary call such as `gpu_sum(4)` runs
  locally. Each remote call is an independent request.

Arguments can be JSON values, bytes, NumPy arrays, or DLPack-compatible tensors.
Pass bytes and tensors as whole arguments. Returned tensors become local NumPy
arrays. `.remote()` raises an exception if execution fails; use `.execute()`
to receive the full `ProgramResult`, including captured output and error details.

See the [Python API](../python-api/index.rst) for details, or
{download}`download a tensor example <../../examples/remote_function.py>`.
For more control over individual instructions, see
[Write a client program](writing-a-program.md).
