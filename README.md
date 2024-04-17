# EdgStr
EdgStr is a software refactoring infrastructure that automatically transforms a two-tier distributed application into its three-tier variant. EdgStr employs powerful profiling as well as code transformation and generation techniques to preserve the original execution semantics, adaptively reduce response latency, and increase processing throughput. It also introduces a replicated state, efficiently synchronized across the edge and the cloud layers by
means of Conflict-Free Replicated Data Types (CRDT).

## Requirements
This codebase was mainly developed with:
- node 12.6.0
- Python 2.7
- [jalangi2](https://github.com/Samsung/jalangi2)
- [z3py](https://github.com/Z3Prover/z3)
- [goreplay](https://github.com/buger/goreplay)
- [CRDT Data Structure](https://github.com/automerge/automerge)  

## Installing Node.js-dependencies
```bash
npm install
```

## Installing python-dependencies
```bash
npm run installing-py
```


# How to run EdgStr?

To determine the Subject S, EdgStr starts by instrumenting all HTTP traffic or [its records](https://github.com/39997gt/EdgStr/blob/master/rec_HTTPs/realty_rec_req_res.gor) between the Client and the Real Subject (or the Node.js Cloud app). EdgStr decodes the parameters of the client’s HTTP requests p<sub>1</sub>,..., p<sub>N</sub> and the return results of the server’s HTTP responses r<sub>1</sub>,..., r<sub>N</sub> and makes a Subject interface.

- To generate an Edge Replica, EdgStr dynamically analyzes a cloud/client-based app.
Originally the server program was running,  
```
node subject_apps/ionic2-realty-rest/server.js
```
- Now the cloud server program is profiled as below.
```bash
RECORD=rec_HTTPs/realty_rec_req_res.gor SERVER=subject_apps/ionic2-realty-rest/server.js npm run EdgeRefactoring 
```
- EdgStr extracts the [Subject.json](https://github.com/39997gt/EdgStr/blob/master/Subject.json) and then starts the instrumentation.
- EdgStr's execution traces include:
  - 1) Datalog facts: RW-Log(s<sub>1</sub>,v<sub>1</sub>,p<sub>1</sub>), RW-Log-Fuzzed(s<sub>1</sub>,1,p<sub>1</sub>), RW-Log-Fuzzed(s<sub>1</sub>,2,p<sub>1</sub>),...
  - 2) Snapshot(Automerge): database, files, and global variables.
- EdgStr represents the server program as Datalog facts and predicates. With the z3 engine, EdgStr solves its constraints of the Subject for the server program not only to generate an Edge replica but also to transform the original Cloud program.

- Briefly run a demo
  - Generated an execution trace script for realty-rest is located in [execution_traces/trace_realty.py](https://github.com/39997gt/EdgStr/blob/master/execution_traces/trace_realty.py).
  - This z3py script will replicate the required remote services of this benchmark for the Subject. Then, EdgStr generates Edge Replica and transforms the original Cloud Program by using [templates](https://github.com/39997gt/EdgStr/tree/master/src/templates).
    ```bash
    python execution_traces/trace_realty.py & node src/js/generator_edge_cloud.js
    ```  
  - 1) [Edge Replica](https://github.com/39997gt/EdgStr/blob/master/results/edge/edge_replica.js)
  - 2) [Transformed Cloud Program](https://github.com/39997gt/EdgStr/blob/master/results/cloud/server/enhanced_norm_property-service.js)
      - [The diff File](https://github.com/39997gt/EdgStr/blob/master/results/difffiles/result.diff)
