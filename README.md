# caroni-demo
A docker based demo of the Caroni Workflow System

## Intro
Caroni is built with the follow tenants:

### Asynchronous
All communication is done via message passing.  This forces a blind eye to
network setups.  Additionally, implicit queueing means that if a host is
temporarily unavailable, "clients" are not hit with a "connection failed".

### Distributed
Possibly the wrong word, but there is no "head", "main", "control" node.  The
workflow managers simply indicate their needs; an agent is meant to serve as
many workflow managers as they (the agents) wish.

### Separation of concerns
As with above, the workflow managers indicate what they want.  However, agents
are in control of their own destiny; their concern is to see their resource
fully utilized.  If a job completes that came from workflow manager A, but the
next job doesn't have it's data transferred yet, the agent will service
something from workflow manager B.

For advanced use cases, an economy of sorts could emerge.

## Setup
Clone this repository if you haven't.
```bash
git clone https://github.com/shaunbrady/caroni-demo.git
cd caroni-demo
```

Create a shared network called caroni_shared_net
```bash
docker network create caroni_shared_net
```

Instantiate the Workflow manager, and then an agent
```bash
docker compose -f "wf-docker-compose.yaml " up 
docker compose -f "agent-docker-compose.yaml " up 
```

Included fixtures will create workflow templates, Job types, as well as
admin:password logins for the various web management interfaces. These fixtures
are ran automatically by the web management instances.

Log in to watch the services create objects, and manage their state.
```bash
http://127.0.0.1:8000/admin for the workflow manager (Watch Workflows/Steps/Dataflows)
http://127.0.0.1:8001/admin for the agent (Watch Jobs/Job Inputs/Job Outputs)
```

Finally, fire off a job. Log in to the site-stub container, and execute a python
script that simulates a website or other actor requesting that the workflow
manager fire off a job of a given type.
```
docker ps | grep sitestub # The first column will be the container id
```
Enter the sitestub container:
```
docker exec -it 8c44404ce4f8 bash
```
At the prompt, simlar to `root@8c44404ce4f8:/app#`
type:
```bash
python site_stub.py caronirocks
```

With in moments (likely seconds), you'll be able to inspect the Workflow reach
the completed state in the workflows manager, and have "prepended
CARONIROCKSappend" in it's output.  Repeat the above command with your own
string, and observe the jobs manipulate it and ultimately return that to the
workflow manager.

## Enjoy!
Caroni will be available as an open source project shortly. Feature requests and
PRs will be welcome!