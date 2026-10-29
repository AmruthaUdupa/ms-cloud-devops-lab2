# Lab 2 — hello-app on Kubernetes

Your copy of this repository is your submission. The lab sheet is on the course site; this file is where you record what you did.

What's here:

| File | What it is |
|---|---|
| `src/`, `index.html` | a small React page that shows which Pod answered |
| `nginx.conf` | serves the page, and answers `/whoami` with the Pod name |
| `Dockerfile` | builds the page, then serves it with nginx on port 80 |

You don't change any of these. You add `hello-app.yaml` and fill in the sections below.

## Before you push, check

- [ ] `hello-app.yaml` is in the root of this repository
- [ ] `screenshot.png` is in the root of this repository
- [ ] every section below is filled in

---

## 1. My image

Docker Hub image I deployed (for example `ravi123/hello-app:v1`):

```
amruthacudupa/hello-app:v1
```

## 2. Pods across nodes

Output of `kubectl get pods -o wide` after you applied `hello-app.yaml`:

```
NAME                         READY   STATUS    RESTARTS   AGE   IP           NODE              NOMINATED NODE   READINESS GATES
hello-app-658f9c78dc-4c7hn   1/1     Running   0          71s   10.244.1.2   desktop-worker2   <none>           <none>
hello-app-658f9c78dc-ftsw5   1/1     Running   0          71s   10.244.2.2   desktop-worker    <none>           <none>
hello-app-658f9c78dc-nxlwn   1/1     Running   0          71s   10.244.2.3   desktop-worker    <none>           <none>
```

## 3. The Service and its endpoints

Output of `kubectl get service hello-app` and `kubectl get endpointslices -l kubernetes.io/service-name=hello-app`:

```
NAME        TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)          AGE
hello-app   LoadBalancer   10.96.58.37   172.18.0.7    8080:32241/TCP   16m

NAME              ADDRESSTYPE   PORTS   ENDPOINTS                                      AGE
hello-app-dpn4t   IPv4          80      10.244.1.5,10.244.2.5,10.244.1.6 + 1 more...   16m
```

## 4. Browser

Add `screenshot.png`: the page at `http://localhost:8080` with at least two Pods in the "Answers so far" table.


## 5. The four traps

For each trap, write what you saw and why it happened, in two or three lines of your own words.

**Trap 1 — the local image.**
Kubernetes could not automatically access an image that existed only on my local machine. I pushed the image to Docker Hub and used the complete image name in the Deployment YAML so Kubernetes could pull it

**Trap 2 — the selector typo.**
When I changed the Service selector from hello-app to helo-app, the EndpointSlice had no endpoints. This happened because the Service selector did not match the labels on the Pods.

**Trap 3 — the wrong targetPort.**
When I changed targetPort from 80 to 8080, the EndpointSlice still showed Pod IP addresses, but the application could not be reached correctly. The container listens on port 80, so I restored targetPort: 80.

**Trap 4 — the EXTERNAL-IP address.**
The LoadBalancer Service showed localhost as its external address in Docker Desktop. I could access the application using http://localhost:8080 because Docker Desktop provides local Kubernetes networking on my computer.

## 6. Your YAML vs the generated YAML

Name two fields in the `--dry-run=client -o yaml` output that were not in your file. Why does your file still work without them?
