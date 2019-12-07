# multiprocessInPython

Recently，I need to write a algorithm whose complexity is O(n^2 k^3 ), fortunately there are a big for loop, which each iteration doesn't influence the other iterations. So I spend a hour in learning multithread (but it behave badly) and multiprocess in Python. Here is a record.



### Target Function

this is the function that you need to execute

```python
def func(arg1,arg2,...):
    a=1
    return a
```



### Pool

Create a process pool， p.apply_async will return the result of func. To collect the result , we need to use .get()

```python
workers=40 # number of workers
batchsize = num_data // workers + 1 if num_data%workers!=0 else num_data//workers

p = Pool(workers)
results = [] # collect the result from func
for i in range(workers):
    data = [x for x in range(i * batchsize, min((i + 1) * batchsize,num_data))]
    results.append(p.apply_async(loop, args=(arg1,arg2,)))

print('Waiting for all subprocesses done...')
p.close()
p.join()
print('All subprocesses done.')

for res in results:
    sub = res.get()
	# some function to join the result of each subprocess
```



## Some Other Tips for optimizing speed

- do not use python's multithread.

- turning a *for iteration* in to a *list generator* won't make it faster.

  ```python
  res=[x for x in range(num)]
  
  # same as 
  res=[]
  for x in range(num):
      res.append(x)
  ```


