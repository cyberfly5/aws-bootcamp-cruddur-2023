# Week 2 — Distributed Tracing

## HoneyComb for Observability
Observability is a strategic imperative for software development. Observability gives developers the confidence to build quickly and deploy more often so that they can deliver more innovation. It also helps teams make critical upgrades and migrations to scale safely and stay ahead of the competition.  
- [HoneyComb](https://www.honeycomb.io/) is one of the tools used for Application Performance and Monitoring
- HoneyComb gives you insights for your applications and makes it easier to troubleshoot
- HoneyComb allows you to quickly analyze highly granular data to discover serious problems before users experience their adverse effects.

Login to ui.honeycomb.io to create an account and an environment

Then grab the API key from your honeycomb account and set Env Vars below:

```sh
export HONEYCOMB_API_KEY=""
export HONEYCOMB_SERVICE_NAME="Cruddur"
gp env HONEYCOMB_API_KEY=""
gp env HONEYCOMB_SERVICE_NAME="Cruddur" *NOT ADVISED* to set this
```
### Add Env Vars to backend-flask in docker compose

OTEL_SERVICE_NAME: 'backend-flask'

We are configuring OTEL (Open Telemetry to send to honeycomb). OTEL is part of Cloud Native Computing Foundation (CNCF) which is the open source, vendor -neutral hub of cloud native computing projects like Kubernetes.
![Alt text](../_docs/assets/otel.png)


OTEL_EXPORTER_OTLP_ENDPOINT: "https://api.honeycomb.io"
OTEL_EXPORTER_OTLP_HEADERS: "x-honeycomb-team=${HONEYCOMB_API_KEY}"

x means custom in this case

### Go to honeycomb Python tab and follow install instructions
1. Install Packages
First, install the Honeycomb OpenTelemetry Distro packages to instrument your application with OpenTelemetry:

```
pip install opentelemetry-api
```
2. Next, install the instrumentation libraries for packages used in your application:
We'll add the following files to our `requirements.txt`

```
opentelemetry-api 
opentelemetry-sdk 
opentelemetry-exporter-otlp-proto-http 
opentelemetry-instrumentation-flask 
opentelemetry-instrumentation-requests
```
3. We'll install these dependencies:

```sh
pip install -r requirements.txt
```
Instrumentation requests are python http calls. Also, it's good practice to add API version for telemetry

4. Add to the `app.py`

```py
from opentelemetry import trace
from opentelemetry.instrumentation.flask import FlaskInstrumentor
from opentelemetry.instrumentation.requests import RequestsInstrumentor
from opentelemetry.exporter.otlp.proto.http.trace_exporter import OTLPSpanExporter
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor
```

```py
# Initialize tracing and an exporter that can send data to Honeycomb
provider = TracerProvider()
processor = BatchSpanProcessor(OTLPSpanExporter())
provider.add_span_processor(processor)
trace.set_tracer_provider(provider)
tracer = trace.get_tracer(__name__)
```
```py
# Initialize automatic instrumentation with Flask
app = Flask(__name__)
FlaskInstrumentor().instrument_app(app)
RequestsInstrumentor().instrument()
```
**cd /frontend-react-js and run npm i**

gitpod /workspace/aws-bootcamp-cruddur-2023/frontend-react-js (main) $ npm i

**Linux distro to consider for Production security**
- Use slim baseline like [Alpine](https://www.alpinelinux.org/) to reduce the attack surface
- Consider redundancy, stability, speed and a secure enviroment

**cd.. and run docker compose up to build the containers**
- Proof of running containers

![Alt text](../_docs/assets/docker-up.png)

![Alt text](../_docs/assets/docker.png)

**Add additional code to 'gitpod.yml' to automatically open ports below:**

![Alt text](../_docs/assets/ports.png)
**click the frontend(3000) link to launch the app**
![Alt text](../_docs/assets/crudder-app.png)
**click the backend(4567) link and append /api/activities/home to see the data**
![Alt text](../_docs/assets/data.png)

### HoneyComb Dataset
- Modified app.py and home_activities to add spans and traces
- Added instrumentation to Honeycomb to add attributes and spans
- Ran a queries for traces and heatmap

**Traces for hard-coded query from home_activities of backend-flask**
![Alt text](../_docs/assets/honeycomb.png)
**Heatmap query example**
![Alt text](../_docs/assets/heatmap.png)
