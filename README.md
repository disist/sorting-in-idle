# Sorting performance

### Motivation: 
Sometimes we need to have 60 fps while we operate with big datasets on client, like we may have large tables and sometimes we need to sort and filter them in a browser. While we are sorting or filtering big datasets we may block the main tread until this operation will be done, so that the user may have a flucky experience (have less than 60/120 fps). 

-----

I created a simple test just to compare the sorting of 1k-1m rows with 50 cols (to emulate a more-less large data set for client) with sorting in main tread: built-in string algorithm, heap, and binary insertion sort algorithm, vs insertion sort algorithm running in browser idle (with using `requestIdleCallback`). 

Binary insertion sort algorithm - is pretty adjustable to break and schedule next run via `requestIdleCallback` with the rest of the unsorted yet array. 

Results: 

| Dataset size  | built-in sort (in main tread) | heap sort (in main tread) | binary insertion sort (in main tread) | binary insertion sort (in idle using `requestIdleCallback`) | 
| ------------- | ------------- | ------------- | ------------- | ------------- | 
| 1k  | 0.52 ms  |  1.16 ms  |  0.69 ms  |  5.63 ms  | 
| 10k  | 3.69 ms  |  5.44 ms  |  2.76 ms  |  41.53 ms  | 
| 100k  | 49.36 ms  | 73.75 ms | 35.02 ms | 477.57 ms |
| 1m  | 1145.2 ms  | 2471.2 ms | 1703.1 ms | 5564.1 ms |

It may be okay: 
- when we do not need sorted data receive fast and we may wait for them as many as we need
- or the dataset is not big, so the difference between sync sort vs sorting in idle maybe not big

Consider a different approach if you need to sort big datasets without blocking the UI, but do it fast, like web worker (but be ready that transferring data main tread <===> to web worker cost some time), or better to move "sort big data sets" from "on the client" to backend level if it's possible. 
