# Configuration

```json
{
    "admin": {
      "address": {
        "socket_address": {
          "address": "127.0.0.1",
          "port_value": 9901
        }
      }
    },
    "static_resources": {
      "listeners": [
        {
          "name": "listener_0",
          "address": {
            "socket_address": {
              "address": "0.0.0.0",
              "port_value": 10000
            }
          },
          "filter_chains": [
            {
              "filters": [
                {
                  "name": "envoy.filters.network.http_connection_manager",
                  "typed_config": {
                    "@type": "type.googleapis.com/envoy.extensions.filters.network.http_connection_manager.v3.HttpConnectionManager",
                    "stat_prefix": "ingress_http",
                    "codec_type": "AUTO",
                    "route_config": {
                      "name": "local_route",
                      "virtual_hosts": [
                        {
                          "name": "local_service",
                          "domains": [
                            "*"
                          ],
                          "routes": [
                            {
                              "match": {
                                "prefix": "/"
                              },
                              "route": {
                                "cluster": "http_service"
                              },
                              "request_headers_to_add": {
                                "header": {"key":"clusterKey", "value":"122222"},
                                "append": true
                              }
                            }
                          ]
                        }
                      ]
                    },
                    "http_filters": [
                      {
                        "name": "api-security",
                        "typed_config": {
                          "@type": "type.googleapis.com/security.SecurityConfig",
                          "clusterKey": "122222",
                          "namespace": "kube-nm",
                          "podName": "httpbin",
                          "urls": "nats://localhost:4222",
                          "subject": "security-api",
                          "clientId": "pod-clientId"
                        }
                      },
                      {
                        "name": "envoy.filters.http.router"
                      }
                    ],
                    "access_log": [
                      {
                        "name": "envoy.access_loggers.file",
                        "typed_config": {
                          "@type": "type.googleapis.com/envoy.extensions.access_loggers.file.v3.FileAccessLog",
                          "path": "/dev/stdout"
                        }
                      }
                    ]
                  }
                }
              ]
            }
          ],
          "listenerFilters": [
            {
              "name": "envoy.filters.listener.original_dst",
              "typedConfig": {
                "@type": "type.googleapis.com/envoy.extensions.filters.listener.original_dst.v3.OriginalDst"
              }
            }
          ]
        }
      ],
      "clusters": [
        {
          "name": "http_service1",
          "connect_timeout": "0.25s",
          "type": "ORIGINAL_DST",
          "lb_policy": "CLUSTER_PROVIDED",
          "protocol_selection": "USE_DOWNSTREAM_PROTOCOL",
          "upstream_bind_config": {
            "source_address": {
              "address": "127.0.0.6",
              "port_value": 0
            }
          }
        },
        {
          "name": "http_service",
          "connect_timeout": "0.25s",
          "type": "STATIC",
          "lb_policy": "ROUND_ROBIN",
          "protocol_selection": "USE_DOWNSTREAM_PROTOCOL",
          "load_assignment": {
            "cluster_name": "http_service",
            "endpoints": [
              {
                "lb_endpoints": [
                  {
                    "endpoint": {
                      "address": {
                        "socket_address": {
                          "address": "127.0.0.1",
                          "port_value": 9090
                        }
                      }
                    }
                  }
                ]
              }
            ]
          }
        }
      ]
    }
  }
```