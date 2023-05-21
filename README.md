# Sorting performance

### Motivation: 
Sometimes we need to have 60 fps while we operate with big datasets on client, like we may have large tables and sometimes we need to sort and filter them in a browser. While we are sorting or filtering big datasets we may block the main tread until this operation will be done, so that the user may have a flucky experience (have less than 60/120 fps). 

-----

I created a simple test just to compare the sorting of 1-100k rows with 50 cols (to emulate a more-less large data set for client) with sorting in main tread: built-in string algorithm, heap, and binary insertion sort algorithm, vs insertion sort algorithm running in browser idle (with using `requestIdleCallback`). 

Binary insertion sort algorithm - is pretty adjustable to break and schedule next run via `requestIdleCallback` with the rest of the unsorted yet array. 

Results: 

| Dataset size  | built-in sort (in main tread) | heap sort (in main tread) | binary insertion sort (in main tread) | binary insertion sort (in idle using `requestIdleCallback`) | 
| ------------- | ------------- | ------------- | ------------- | ------------- | 
| 1k  | 0.52685546875 ms  |  1.1611328125 ms  |  0.69091796875 ms  |  5.6328125 ms  | 
| 10k  | 3.69580078125 ms  |  5.444091796875 ms  |  2.76171875 ms  |  41.531982421875 ms  | 
| 100k  | 49.3642578125 ms  | 73.7529296875 ms | 35.02001953125 ms | 477.573974609375 ms |
| 1m  | 1145.22412109375 ms  | 2471.2119140625 ms | 1703.04296875 ms | 5564.070068359375 ms |

It may be okay: 
- when we do not need sorted data receive fast and we may wait for them as many as we need
- or the dataset is not big, so the difference between sync sort vs sorting in idle maybe not big

Consider a different approach if you need to sort big datasets without blocking the UI, but do it fast, like web worker (but be ready that transferring data main tread <===> to web worker cost some time), or better to move "sort big data sets" from "on the client" to backend level as usual. 
