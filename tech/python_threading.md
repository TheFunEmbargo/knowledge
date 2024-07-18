# Threading


[Great resource](https://superfastpython.com/threading-in-python/)

# Example classes


Predetermined list of tasks
```python
class Task:
    def __init__(self, tid: int, func: callable, *args, **kwargs) -> None:
        """Define a task with ID, function, then args or kwargs for that function"""
        self.tid = tid
        self.func = func
        self.args = args
        self.kwargs = kwargs

    def run(self):
        return self.func(*self.args, **self.kwargs)


class Manager:
    def __init__(self) -> None:
        self.max_workers = int(os.getenv("MAX_WORKERS") or 1)

    def run_tasks(self, tasks: list[Task]) -> tuple[any, any]:
        """runs tasks & returns tuple of (identifier, result)"""
        with ThreadPoolExecutor(max_workers=self.max_workers) as executor:
            futures = {executor.submit(task.run): task.tid for task in tasks}
            for future in as_completed(futures):
                try:
                    tid = futures[future]
                    result = future.result()
                except Exception as exc:
                    raise ValueError(f"Error running task {tid}: {exc}")
                yield (tid, result)
```
