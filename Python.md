# Python Notes

## Lazy Initialization

what if creating an instance variable is expensive? do I still have to initialize it in the init function?

If creating an instance variable is expensive, you may want to delay its initialization until it is actually needed.
In such cases, you can define the instance variable as None in the `__init__()` method and then initialize it lazily
when it is first accessed.

If creating an instance of ExpensiveObject is expensive, you can delay the initialization of self.data by setting it to
`None` in `__init__()`:

```python
class ExpensiveObject:
    def __init__(self):
        self.data = None

    def get_data(self):
        if self.data is None:
            # Expensive initialization
            self.data = ...
        return self.data
```

In this example, the `get_data()` method initializes `self.data` if it is `None`, and returns the initialized value. If
`get_data()` is never called, `self.data` remains `None`, and the expensive initialization is never performed.

This approach is known as lazy initialization, and it is a common technique for optimizing performance in
object-oriented programming. It allows you to defer expensive operations until they are actually needed, which can
reduce the overall cost of object creation and improve the responsiveness of your application.

## Virtual environment

To activate a Python virtual environment in Linux, follow these steps:

1. Open a terminal window on your Linux machine.
2. Navigate to the directory where you created your virtual environment.
3. Run the following command to activate the virtual environment:
```bash
source <venv>/bin/activate
```
To deactivate the virtual environment, simply run the following command:
```bash
deactivate
```