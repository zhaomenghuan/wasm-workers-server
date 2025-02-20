---
sidebar_position: 3
---

# Python

The [Python](https://www.python.org/) interpreter is not embedded in Wasm Workers Server. To create workers based on this language, you first need to install a Python runtime.

Fortunately, we provide precompiled `python.wasm` modules in our [WebAssembly Language Runtimes](https://github.com/vmware-labs/webassembly-language-runtimes/) project, so the installation is simple:

## Installation

To install the Python Wasm module, run the following command:

```
wws runtimes install python latest
```

## Your first Python worker

Python workers are based on the [Request](https://developer.mozilla.org/en-US/docs/Web/API/Request) / [Response](https://developer.mozilla.org/en-US/docs/Web/API/Response) objects from the Web Fetch API. Since these entities don't exist in the Python language, the worker includes a polyfill with these two classes. You can find the [polyfill code here](#).

In this example, the worker will get a request and print all the related information.

1. First, create a new `index.py` file with the following content. The `worker` method is mandatory as it will be the entrypoint for the worker:

    ```python title="./index.py"
    def worker(req):
        return Response("Hello from Python in WebAssembly!")
    ```

1. Now, you can add more content to the `worker` method to show the request information. In addition to that, let's add a response header.

    ```python title="./index.py"
    def worker(req):
        # Body response
        body = '''\
            <!DOCTYPE html>
            <body>
            <h1>Hello from Wasm Workers Server</h1>
            <p>Replying to {url}</p>
            <p>Method: {method}</p>
            <p>User Agent: {agent}</p
            <p>Payload: {body}</p>
            <p>
                This page was generated by a Python file inside WebAssembly
            </p>
            </body>
        '''.format(
            url=req.url,
            method=req.method,
            agent=req.headers["user-agent"],
            body=req.body
        )

        # Build a new response
        res = Response(body)

        # Add a new header
        res.headers["x-generated-by"] = "wasm-workers-server"

        return res
    ```

1. Save the file
1. If you didn't download the `wws` server yet, check our [Getting Started](../get-started/quickstart.md) guide.
1. [Install the Python runtime](#installation)
1. Run your worker with `wws`

    ```bash
    wws

    ⚙️ Loading routes from: .
    🗺 Detected routes:
        - http://127.0.0.1:8080/
        => index.py (name: default)
    🚀 Start serving requests at http://127.0.0.1:8080
    ```

1. Finally, open <http://127.0.0.1:8080> in your browser.