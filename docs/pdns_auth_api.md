# Configuration

To get PowerDNS Authoritative API statistics, you need to enable it as described [here](https://doc.powerdns.com/authoritative/http-api/index.html#enabling-the-api).
To export these metrics, we will use [json_exporter](https://github.com/prometheus-community/json_exporter).

Here is the configuration needed for the exporter:

```yaml
---
modules:
  pdns:
    # If authentication is enabled
    # headers:
    #   X-API-Key: <YOUR_API_KEY>
    metrics:
      - name: pdns_auth_api_response_by_qtype
        type: object
        path: '{[?(@.name=="response-by-qtype")].value[*]}'
        help: 'PowerDNS Authoritative response by qtype value'
        labels:
          name: '{ .name }'
        values:
          value: '{ .value }'
      - name: pdns_auth_api_response_by_rcode
        type: object
        path: '{[?(@.name=="response-by-rcode")].value[*]}'
        help: 'PowerDNS Authoritative response by rcode value'
        labels:
          name: '{ .name }'
        values:
          value: '{ .value }'
      - name: pdns_auth_api_response_sizes
        type: object
        path: '{[?(@.name=="response-sizes")].value[*]}'
        help: 'PowerDNS Authoritative response sizes value'
        labels:
          name: '{ .name }'
        values:
          value: '{ .value }'
```

To gather metrics, use this URL: `http://localhost:7979/probe?module=pdns&target=http://localhost:8081/api/v1/servers/localhost/statistics`.

Don't forget to also use `relabel_configs` in your Prometheus configuration to map the `target` parameter as the `instance` label.
