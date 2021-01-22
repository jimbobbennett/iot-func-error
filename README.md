# Reproduction of an error using the Azure IoT Python SDK in an Azure Functions app on an Apple M1 Mac

# Steps to reproduce the error

* Install the Azure Functions CLI using HomeBrew under Rosetta 2

    ```output
    Core Tools Version:       3.0.3233 Commit hash: d1772f733802122a326fa696dd4c086292ec0171 
    Function Runtime Version: 3.0.15193.0
    ```

* Create a new Python Virtual Environment using Python 3.8:

    ```sh
    /usr/bin/python3 -m venv .venv
    ```

* Activate the Virtual Environment:

    ```sh
    source .venv/bin/activate
    ```

* From a new folder, create a new functions app:

    ```sh
    func init --source-control true --worker-runtime python
    ```

* Create a new HTTP trigger:

    ```sh
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```

* Edit the `requirements.txt` file and add the `azure-iot-hub` package

* Install the packages:

    ```sh
    pip install -r requirements.txt
    ```

    > You may need to install CMAKE via HomeBrew if it's not installed on your Mac

* Edit the `__init__.py` file in the `HttpExample` folder , and add the following import below the existing imports:

    ```python
    from azure.iot.hub import IoTHubRegistryManager
    ```

* Start the functions app:

    ```sh
    func start
    ```

## Expected

* The functions host should start pretty quickly and host the HTTP trigger

## Actual

* The functions runtime starts *really* slowly (minutes instead of seconds)
* The following error appears:

    ```output
    [2021-01-22T00:04:13.014Z] Result: Failure
    [2021-01-22T00:04:13.014Z] Exception: ImportError: dlopen(/Users/jimbennett/GitHub/iot-func-error/.venv/lib/python3.8/site-packages/uamqp/c_uamqp.cpython-38-darwin.so, 2): Symbol not found: _SASToken_CreateString
    [2021-01-22T00:04:13.014Z]   Referenced from: /Users/jimbennett/GitHub/iot-func-error/.venv/lib/python3.8/site-packages/uamqp/c_uamqp.cpython-38-darwin.so
    [2021-01-22T00:04:13.014Z]   Expected in: flat namespace
    [2021-01-22T00:04:13.014Z]  in /Users/jimbennett/GitHub/iot-func-error/.venv/lib/python3.8/site-packages/uamqp/c_uamqp.cpython-38-darwin.so. Troubleshooting Guide: https://aka.ms/functions-modulenotfound
    [2021-01-22T00:04:13.014Z] Stack:   File "/usr/local/Cellar/azure-functions-core-tools@3/3.0.3233/workers/python/3.8/OSX/X64/azure_functions_worker/dispatcher.py", line 271, in _handle__function_load_request
    [2021-01-22T00:04:13.014Z]     func = loader.load_function(
    [2021-01-22T00:04:13.014Z]   File "/usr/local/Cellar/azure-functions-core-tools@3/3.0.3233/workers/python/3.8/OSX/X64/azure_functions_worker/utils/wrappers.py", line 34, in call
    [2021-01-22T00:04:13.015Z]     raise extend_exception_message(e, message)
    [2021-01-22T00:04:13.015Z]   File "/usr/local/Cellar/azure-functions-core-tools@3/3.0.3233/workers/python/3.8/OSX/X64/azure_functions_worker/utils/wrappers.py", line 32, in call
    [2021-01-22T00:04:13.015Z]     return func(*args, **kwargs)
    [2021-01-22T00:04:13.015Z]   File "/usr/local/Cellar/azure-functions-core-tools@3/3.0.3233/workers/python/3.8/OSX/X64/azure_functions_worker/loader.py", line 76, in load_function
    [2021-01-22T00:04:13.015Z]     mod = importlib.import_module(fullmodname)
    [2021-01-22T00:04:13.015Z]   File "/Library/Developer/CommandLineTools/Library/Frameworks/Python3.framework/Versions/3.8/lib/python3.8/importlib/__init__.py", line 127, in import_module
    [2021-01-22T00:04:13.015Z]     return _bootstrap._gcd_import(name[level:], package, level)
    [2021-01-22T00:04:13.015Z]   File "<frozen importlib._bootstrap>", line 1014, in _gcd_import
    [2021-01-22T00:04:13.015Z]   File "<frozen importlib._bootstrap>", line 991, in _find_and_load
    [2021-01-22T00:04:13.015Z]   File "<frozen importlib._bootstrap>", line 961, in _find_and_load_unlocked
    [2021-01-22T00:04:13.015Z]   File "<frozen importlib._bootstrap>", line 219, in _call_with_frames_removed
    [2021-01-22T00:04:13.015Z]   File "<frozen importlib._bootstrap>", line 1014, in _gcd_import
    [2021-01-22T00:04:13.015Z]   File "<frozen importlib._bootstrap>", line 991, in _find_and_load
    [2021-01-22T00:04:13.015Z]   File "<frozen importlib._bootstrap>", line 975, in _find_and_load_unlocked
    [2021-01-22T00:04:13.015Z]   File "<frozen importlib._bootstrap>", line 671, in _load_unlocked
    [2021-01-22T00:04:13.015Z]   File "<frozen importlib._bootstrap_external>", line 783, in exec_module
    [2021-01-22T00:04:13.015Z]   File "<frozen importlib._bootstrap>", line 219, in _call_with_frames_removed
    [2021-01-22T00:04:13.015Z]   File "/Users/jimbennett/GitHub/iot-func-error/HttpExample/__init__.py", line 5, in <module>
    [2021-01-22T00:04:13.015Z]     from azure.iot.hub import IoTHubRegistryManager
    [2021-01-22T00:04:13.015Z]   File "/Users/jimbennett/GitHub/iot-func-error/.venv/lib/python3.8/site-packages/azure/iot/hub/__init__.py", line 6, in <module>
    [2021-01-22T00:04:13.015Z]     from .iothub_registry_manager import IoTHubRegistryManager
    [2021-01-22T00:04:13.015Z]   File "/Users/jimbennett/GitHub/iot-func-error/.venv/lib/python3.8/site-packages/azure/iot/hub/iothub_registry_manager.py", line 6, in <module>
    [2021-01-22T00:04:13.015Z]     from .iothub_amqp_client import IoTHubAmqpClient as iothub_amqp_client
    [2021-01-22T00:04:13.015Z]   File "/Users/jimbennett/GitHub/iot-func-error/.venv/lib/python3.8/site-packages/azure/iot/hub/iothub_amqp_client.py", line 22, in <module>
    [2021-01-22T00:04:13.015Z]     import uamqp
    [2021-01-22T00:04:13.015Z]   File "/Users/jimbennett/GitHub/iot-func-error/.venv/lib/python3.8/site-packages/uamqp/__init__.py", line 12, in <module>
    [2021-01-22T00:04:13.015Z]     from uamqp import c_uamqp  # pylint: disable=import-self
    [2021-01-22T00:04:13.015Z] .
    ```