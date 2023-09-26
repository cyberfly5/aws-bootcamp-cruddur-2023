# Week 2 — Distributed Tracing

## HoneyComb

Login to ui.honeycomb.io and create and account and an environment

Then grab the API key from your honeycomb account and set Env Vars below:

```sh
export HONEYCOMB_API_KEY=""
export HONEYCOMB_SERVICE_NAME="Cruddur"
gp env HONEYCOMB_API_KEY=""
gp env HONEYCOMB_SERVICE_NAME="Cruddur" *NOT ADVISED* to set this
```
**Add Env Vars to backend-flask in docker compose**

OTEL_SERVICE_NAME: 'backend-flask'

We are configuring OTEL (Open Telemetry to send to honeycomb). OTEL is part of Cloud Native Computing Foundation (CNCF) which is the open source, vendor -neutral hub of cloud native computing projects like Kubernetes.
![Alt text](../_docs/assets/otel.png)


OTEL_EXPORTER_OTLP_ENDPOINT: "https://api.honeycomb.io"
OTEL_EXPORTER_OTLP_HEADERS: "x-honeycomb-team=${HONEYCOMB_API_KEY}"

x means custom in this case

**Go to honeycomb Python tab and follow install instructions**
1. Install Packages
First, install the Honeycomb OpenTelemetry Distro packages to instrument your application with OpenTelemetry:

```
pip install opentelemetry-api
```
Next, install the instrumentation libraries for packages used in your application:
We'll add the following files to our `requirements.txt`

```
opentelemetry-api 
opentelemetry-sdk 
opentelemetry-exporter-otlp-proto-http 
opentelemetry-instrumentation-flask 
opentelemetry-instrumentation-requests
```
We'll install these dependencies:

```sh
pip install -r requirements.txt
```
Instrumentation requests are python http calls. Also, it's good practice to add API version for telemetry


Add to the `app.py`

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
