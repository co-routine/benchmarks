# benchmarks
coroutine 用ベンチマーク資料

ベンチマークコマンド
- [wrk](https://github.com/wg/wrk)

## Python

pip install list

- [gunicorn](https://github.com/benoitc/gunicorn)
- [meinheld](https://github.com/mopemope/meinheld)

```python
import flask
import bottle

app = flask.Flask(__name__)
bottle_app = bottle.app()

@bottle_app.route('/')
@app.route('/')
def index():
    return b"hello, world"

def wsgi(env, res):
    data = b"hello, world"
    status = "200 OK"
    headers = [
        ("Content-type", "text/plain"),
        ("Content-Length", str(len(data)))
    ]
    res(status, headers)
    return iter([data])
```

```
$ gunicorn --pythonpath app.py -k meinheld.gmeinheld.MeinheldWorker -b :6000 -w 4 app:wsgi -D
$ wrk -c10 -d10 http://localhost:6000
Running 10s test @ http://localhost:6000
  2 threads and 10 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency   447.50us    1.88ms  31.14ms   95.34%
    Req/Sec    58.41k     9.30k   70.23k    74.50%
  1163621 requests in 10.02s, 179.77MB read
Requests/sec: 116159.03
Transfer/sec:     17.95MB

$ gunicorn --pythonpath app.py -k meinheld.gmeinheld.MeinheldWorker -b :6000 -w 4 app:bottle_app -D
$ wrk -c10 -d10 http://localhost:6000
Running 10s test @ http://localhost:6000
  2 threads and 10 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency   575.18us    1.75ms  35.30ms   95.93%
    Req/Sec    18.40k     3.49k   26.41k    66.50%
  366606 requests in 10.02s, 61.53MB read
Requests/sec:  36573.71
Transfer/sec:      6.14MB


$ gunicorn --pythonpath app.py -k meinheld.gmeinheld.MeinheldWorker -b :6000 -w 4 app:app -D
$ wrk -c10 -d10 http://localhost:6000
Running 10s test @ http://localhost:6000
  2 threads and 10 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     1.06ms    1.47ms  24.32ms   95.66%
    Req/Sec     5.81k     1.46k    8.87k    61.00%
  115793 requests in 10.03s, 19.44MB read
Requests/sec:  11544.30
Transfer/sec:      1.94MB
```
