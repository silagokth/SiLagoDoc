# Sylva Input Format

## App Graph

The input to Sylva is a SDF graph. The SDF graph is a directed graph where each node represents a task and each edge represents a data dependency between tasks. The SDF graph is represented in the form of a JSON file. The following json snippet is an example of AppGraph that represent the _sobel_ application.

```json
{
  "nodes": [
    {
      "id": "load",
      "func": "func_load",
      "outputPorts": [
        {
          "id": "load_output",
          "rate": 1,
          "tokenSize": 16
        }
      ]
    },
    {
      "id": "copy",
      "func": "func_copy",
      "inputPorts": [
        {
          "id": "copy_input",
          "rate": 1,
          "tokenSize": 16
        }
      ],
      "outputPorts": [
        {
          "id": "copy_output_0",
          "rate": 1,
          "tokenSize": 16
        },
        {
          "id": "copy_output_1",
          "rate": 1,
          "tokenSize": 16
        }
      ]
    },
    {
      "id": "gx",
      "func": "func_gx",
      "inputPorts": [
        {
          "id": "gx_input",
          "rate": 1,
          "tokenSize": 16
        }
      ],
      "outputPorts": [
        {
          "id": "gx_output",
          "rate": 1,
          "tokenSize": 16
        }
      ]
    },
    {
      "id": "gy",
      "func": "func_gy",
      "inputPorts": [
        {
          "id": "gy_input",
          "rate": 1,
          "tokenSize": 16
        }
      ],
      "outputPorts": [
        {
          "id": "gy_output",
          "rate": 1,
          "tokenSize": 16
        }
      ]
    },
    {
      "id": "combine",
      "func": "func_combine",
      "inputPorts": [
        {
          "id": "combine_input_0",
          "rate": 1,
          "tokenSize": 16
        },
        {
          "id": "combine_input_1",
          "rate": 1,
          "tokenSize": 16
        }
      ],
      "outputPorts": [
        {
          "id": "combine_output",
          "rate": 1,
          "tokenSize": 16
        }
      ]
    },
    {
      "id": "store",
      "func": "func_store",
      "inputPorts": [
        {
          "id": "store_input",
          "rate": 1,
          "tokenSize": 16
        }
      ]
    }
  ],
  "edges": [
    {
      "id": "edge_load_copy",
      "sourceNode": "load",
      "targetNode": "copy",
      "sourcePort": "load_output",
      "targetPort": "copy_input",
      "tokenSize": 16
    },
    {
      "id": "edge_copy_gx",
      "sourceNode": "copy",
      "targetNode": "gx",
      "sourcePort": "copy_output_0",
      "targetPort": "gx_input",
      "tokenSize": 16
    },
    {
      "id": "edge_copy_gy",
      "sourceNode": "copy",
      "targetNode": "gy",
      "sourcePort": "copy_output_1",
      "targetPort": "gy_input",
      "tokenSize": 16
    },
    {
      "id": "edge_gx_combine",
      "sourceNode": "gx",
      "targetNode": "combine",
      "sourcePort": "gx_output",
      "targetPort": "combine_input_0",
      "tokenSize": 16
    },
    {
      "id": "edge_gy_combine",
      "sourceNode": "gy",
      "targetNode": "combine",
      "sourcePort": "gy_output",
      "targetPort": "combine_input_1",
      "tokenSize": 16
    },
    {
      "id": "edge_combine_store",
      "sourceNode": "combine",
      "targetNode": "store",
      "sourcePort": "combine_output",
      "targetPort": "store_input",
      "tokenSize": 16
    }
  ]
}
```

## Alimp Lirary

The Alimp library is a collection of functions that can implement the functionality of the nodes in the SDF graph. Each function in the Alimp library is represented as a JSON object. The following json snippet is an example of Alimp library that represent the _sobel_ application.

```json
{
  "entries": [
    {
      "func": "func_load",
      "instances": [
        {
          "width": 2,
          "height": 2,
          "latency": 16,
          "energy": 1,
          "outputAddrTimePatterns": [
            {},
            {
              "key": 1,
              "value": 1
            },
            {
              "key": 2,
              "value": 2
            },
            {
              "key": 3,
              "value": 3
            },
            {
              "key": 4,
              "value": 4
            },
            {
              "key": 5,
              "value": 5
            },
            {
              "key": 6,
              "value": 6
            },
            {
              "key": 7,
              "value": 7
            },
            {
              "key": 8,
              "value": 8
            },
            {
              "key": 9,
              "value": 9
            },
            {
              "key": 10,
              "value": 10
            },
            {
              "key": 11,
              "value": 11
            },
            {
              "key": 12,
              "value": 12
            },
            {
              "key": 13,
              "value": 13
            },
            {
              "key": 14,
              "value": 14
            },
            {
              "key": 15,
              "value": 15
            }
          ]
        },
        {
          "width": 4,
          "height": 4,
          "latency": 4,
          "energy": 2,
          "outputAddrTimePatterns": [
            {},
            {
              "key": 1
            },
            {
              "key": 2
            },
            {
              "key": 3
            },
            {
              "key": 4,
              "value": 1
            },
            {
              "key": 5,
              "value": 1
            },
            {
              "key": 6,
              "value": 1
            },
            {
              "key": 7,
              "value": 1
            },
            {
              "key": 8,
              "value": 2
            },
            {
              "key": 9,
              "value": 2
            },
            {
              "key": 10,
              "value": 2
            },
            {
              "key": 11,
              "value": 2
            },
            {
              "key": 12,
              "value": 3
            },
            {
              "key": 13,
              "value": 3
            },
            {
              "key": 14,
              "value": 3
            },
            {
              "key": 15,
              "value": 3
            }
          ]
        }
      ]
    },
    {
      "func": "func_copy",
      "instances": [
        {
          "width": 1,
          "height": 1,
          "latency": 17,
          "energy": 1,
          "inputAddrTimePatterns": [
            {},
            {
              "key": 1,
              "value": 1
            },
            {
              "key": 2,
              "value": 2
            },
            {
              "key": 3,
              "value": 3
            },
            {
              "key": 4,
              "value": 4
            },
            {
              "key": 5,
              "value": 5
            },
            {
              "key": 6,
              "value": 6
            },
            {
              "key": 7,
              "value": 7
            },
            {
              "key": 8,
              "value": 8
            },
            {
              "key": 9,
              "value": 9
            },
            {
              "key": 10,
              "value": 10
            },
            {
              "key": 11,
              "value": 11
            },
            {
              "key": 12,
              "value": 12
            },
            {
              "key": 13,
              "value": 13
            },
            {
              "key": 14,
              "value": 14
            },
            {
              "key": 15,
              "value": 15
            }
          ],
          "outputAddrTimePatterns": [
            {
              "value": 1
            },
            {
              "key": 1,
              "value": 2
            },
            {
              "key": 2,
              "value": 3
            },
            {
              "key": 3,
              "value": 4
            },
            {
              "key": 4,
              "value": 5
            },
            {
              "key": 5,
              "value": 6
            },
            {
              "key": 6,
              "value": 7
            },
            {
              "key": 7,
              "value": 8
            },
            {
              "key": 8,
              "value": 9
            },
            {
              "key": 9,
              "value": 10
            },
            {
              "key": 10,
              "value": 11
            },
            {
              "key": 11,
              "value": 12
            },
            {
              "key": 12,
              "value": 13
            },
            {
              "key": 13,
              "value": 14
            },
            {
              "key": 14,
              "value": 15
            },
            {
              "key": 15,
              "value": 16
            },
            {
              "key": 16,
              "value": 1
            },
            {
              "key": 17,
              "value": 2
            },
            {
              "key": 18,
              "value": 3
            },
            {
              "key": 19,
              "value": 4
            },
            {
              "key": 20,
              "value": 5
            },
            {
              "key": 21,
              "value": 6
            },
            {
              "key": 22,
              "value": 7
            },
            {
              "key": 23,
              "value": 8
            },
            {
              "key": 24,
              "value": 9
            },
            {
              "key": 25,
              "value": 10
            },
            {
              "key": 26,
              "value": 11
            },
            {
              "key": 27,
              "value": 12
            },
            {
              "key": 28,
              "value": 13
            },
            {
              "key": 29,
              "value": 14
            },
            {
              "key": 30,
              "value": 15
            },
            {
              "key": 31,
              "value": 16
            }
          ]
        },
        {
          "width": 2,
          "height": 1,
          "latency": 9,
          "energy": 2,
          "inputAddrTimePatterns": [
            {},
            {
              "key": 1
            },
            {
              "key": 2,
              "value": 1
            },
            {
              "key": 3,
              "value": 1
            },
            {
              "key": 4,
              "value": 2
            },
            {
              "key": 5,
              "value": 2
            },
            {
              "key": 6,
              "value": 3
            },
            {
              "key": 7,
              "value": 3
            },
            {
              "key": 8,
              "value": 4
            },
            {
              "key": 9,
              "value": 4
            },
            {
              "key": 10,
              "value": 5
            },
            {
              "key": 11,
              "value": 5
            },
            {
              "key": 12,
              "value": 6
            },
            {
              "key": 13,
              "value": 6
            },
            {
              "key": 14,
              "value": 7
            },
            {
              "key": 15,
              "value": 7
            }
          ],
          "outputAddrTimePatterns": [
            {
              "value": 1
            },
            {
              "key": 1,
              "value": 1
            },
            {
              "key": 2,
              "value": 2
            },
            {
              "key": 3,
              "value": 2
            },
            {
              "key": 4,
              "value": 3
            },
            {
              "key": 5,
              "value": 3
            },
            {
              "key": 6,
              "value": 4
            },
            {
              "key": 7,
              "value": 4
            },
            {
              "key": 8,
              "value": 5
            },
            {
              "key": 9,
              "value": 5
            },
            {
              "key": 10,
              "value": 6
            },
            {
              "key": 11,
              "value": 6
            },
            {
              "key": 12,
              "value": 7
            },
            {
              "key": 13,
              "value": 7
            },
            {
              "key": 14,
              "value": 8
            },
            {
              "key": 15,
              "value": 8
            },
            {
              "key": 16,
              "value": 9
            },
            {
              "key": 17,
              "value": 9
            },
            {
              "key": 18,
              "value": 10
            },
            {
              "key": 19,
              "value": 10
            },
            {
              "key": 20,
              "value": 11
            },
            {
              "key": 21,
              "value": 11
            },
            {
              "key": 22,
              "value": 12
            },
            {
              "key": 23,
              "value": 12
            },
            {
              "key": 24,
              "value": 13
            },
            {
              "key": 25,
              "value": 13
            },
            {
              "key": 26,
              "value": 14
            },
            {
              "key": 27,
              "value": 14
            },
            {
              "key": 28,
              "value": 15
            },
            {
              "key": 29,
              "value": 15
            },
            {
              "key": 30,
              "value": 16
            },
            {
              "key": 31,
              "value": 16
            }
          ]
        },
        {
          "width": 4,
          "height": 1,
          "latency": 5,
          "energy": 4,
          "inputAddrTimePatterns": [
            {},
            {
              "key": 1
            },
            {
              "key": 2
            },
            {
              "key": 3
            },
            {
              "key": 4,
              "value": 1
            },
            {
              "key": 5,
              "value": 1
            },
            {
              "key": 6,
              "value": 1
            },
            {
              "key": 7,
              "value": 1
            },
            {
              "key": 8,
              "value": 2
            },
            {
              "key": 9,
              "value": 2
            },
            {
              "key": 10,
              "value": 2
            },
            {
              "key": 11,
              "value": 2
            },
            {
              "key": 12,
              "value": 3
            },
            {
              "key": 13,
              "value": 3
            },
            {
              "key": 14,
              "value": 3
            },
            {
              "key": 15,
              "value": 3
            }
          ],
          "outputAddrTimePatterns": [
            {
              "value": 1
            },
            {
              "key": 1,
              "value": 1
            },
            {
              "key": 2,
              "value": 1
            },
            {
              "key": 3,
              "value": 1
            },
            {
              "key": 4,
              "value": 2
            },
            {
              "key": 5,
              "value": 2
            },
            {
              "key": 6,
              "value": 2
            },
            {
              "key": 7,
              "value": 2
            },
            {
              "key": 8,
              "value": 3
            },
            {
              "key": 9,
              "value": 3
            },
            {
              "key": 10,
              "value": 3
            },
            {
              "key": 11,
              "value": 3
            },
            {
              "key": 12,
              "value": 4
            },
            {
              "key": 13,
              "value": 4
            },
            {
              "key": 14,
              "value": 4
            },
            {
              "key": 15,
              "value": 4
            },
            {
              "key": 16,
              "value": 5
            },
            {
              "key": 17,
              "value": 5
            },
            {
              "key": 18,
              "value": 5
            },
            {
              "key": 19,
              "value": 5
            },
            {
              "key": 20,
              "value": 6
            },
            {
              "key": 21,
              "value": 6
            },
            {
              "key": 22,
              "value": 6
            },
            {
              "key": 23,
              "value": 6
            },
            {
              "key": 24,
              "value": 7
            },
            {
              "key": 25,
              "value": 7
            },
            {
              "key": 26,
              "value": 7
            },
            {
              "key": 27,
              "value": 7
            },
            {
              "key": 28,
              "value": 8
            },
            {
              "key": 29,
              "value": 8
            },
            {
              "key": 30,
              "value": 8
            },
            {
              "key": 31,
              "value": 8
            }
          ]
        }
      ]
    },
    {
      "func": "func_gx",
      "instances": [
        {
          "width": 4,
          "height": 4,
          "latency": 17,
          "energy": 10,
          "inputAddrTimePatterns": [
            {},
            {
              "key": 1,
              "value": 1
            },
            {
              "key": 2,
              "value": 2
            },
            {
              "key": 3,
              "value": 3
            },
            {
              "key": 4,
              "value": 4
            },
            {
              "key": 5,
              "value": 5
            },
            {
              "key": 6,
              "value": 6
            },
            {
              "key": 7,
              "value": 7
            },
            {
              "key": 8,
              "value": 8
            },
            {
              "key": 9,
              "value": 9
            },
            {
              "key": 10,
              "value": 10
            },
            {
              "key": 11,
              "value": 11
            },
            {
              "key": 12,
              "value": 12
            },
            {
              "key": 13,
              "value": 13
            },
            {
              "key": 14,
              "value": 14
            },
            {
              "key": 15,
              "value": 15
            }
          ],
          "outputAddrTimePatterns": [
            {
              "value": 1
            },
            {
              "key": 1,
              "value": 2
            },
            {
              "key": 2,
              "value": 3
            },
            {
              "key": 3,
              "value": 4
            },
            {
              "key": 4,
              "value": 5
            },
            {
              "key": 5,
              "value": 6
            },
            {
              "key": 6,
              "value": 7
            },
            {
              "key": 7,
              "value": 8
            },
            {
              "key": 8,
              "value": 9
            },
            {
              "key": 9,
              "value": 10
            },
            {
              "key": 10,
              "value": 11
            },
            {
              "key": 11,
              "value": 12
            },
            {
              "key": 12,
              "value": 13
            },
            {
              "key": 13,
              "value": 14
            },
            {
              "key": 14,
              "value": 15
            },
            {
              "key": 15,
              "value": 16
            }
          ]
        }
      ]
    },
    {
      "func": "func_gy",
      "instances": [
        {
          "width": 4,
          "height": 4,
          "latency": 17,
          "energy": 10,
          "inputAddrTimePatterns": [
            {},
            {
              "key": 1,
              "value": 1
            },
            {
              "key": 2,
              "value": 2
            },
            {
              "key": 3,
              "value": 3
            },
            {
              "key": 4,
              "value": 4
            },
            {
              "key": 5,
              "value": 5
            },
            {
              "key": 6,
              "value": 6
            },
            {
              "key": 7,
              "value": 7
            },
            {
              "key": 8,
              "value": 8
            },
            {
              "key": 9,
              "value": 9
            },
            {
              "key": 10,
              "value": 10
            },
            {
              "key": 11,
              "value": 11
            },
            {
              "key": 12,
              "value": 12
            },
            {
              "key": 13,
              "value": 13
            },
            {
              "key": 14,
              "value": 14
            },
            {
              "key": 15,
              "value": 15
            }
          ],
          "outputAddrTimePatterns": [
            {
              "value": 1
            },
            {
              "key": 1,
              "value": 2
            },
            {
              "key": 2,
              "value": 3
            },
            {
              "key": 3,
              "value": 4
            },
            {
              "key": 4,
              "value": 5
            },
            {
              "key": 5,
              "value": 6
            },
            {
              "key": 6,
              "value": 7
            },
            {
              "key": 7,
              "value": 8
            },
            {
              "key": 8,
              "value": 9
            },
            {
              "key": 9,
              "value": 10
            },
            {
              "key": 10,
              "value": 11
            },
            {
              "key": 11,
              "value": 12
            },
            {
              "key": 12,
              "value": 13
            },
            {
              "key": 13,
              "value": 14
            },
            {
              "key": 14,
              "value": 15
            },
            {
              "key": 15,
              "value": 16
            }
          ]
        }
      ]
    },
    {
      "func": "func_combine",
      "instances": [
        {
          "width": 2,
          "height": 2,
          "latency": 17,
          "energy": 2,
          "inputAddrTimePatterns": [
            {
              "value": 1
            },
            {
              "key": 1,
              "value": 2
            },
            {
              "key": 2,
              "value": 3
            },
            {
              "key": 3,
              "value": 4
            },
            {
              "key": 4,
              "value": 5
            },
            {
              "key": 5,
              "value": 6
            },
            {
              "key": 6,
              "value": 7
            },
            {
              "key": 7,
              "value": 8
            },
            {
              "key": 8,
              "value": 9
            },
            {
              "key": 9,
              "value": 10
            },
            {
              "key": 10,
              "value": 11
            },
            {
              "key": 11,
              "value": 12
            },
            {
              "key": 12,
              "value": 13
            },
            {
              "key": 13,
              "value": 14
            },
            {
              "key": 14,
              "value": 15
            },
            {
              "key": 15,
              "value": 16
            },
            {
              "key": 16
            },
            {
              "key": 17,
              "value": 1
            },
            {
              "key": 18,
              "value": 2
            },
            {
              "key": 19,
              "value": 3
            },
            {
              "key": 20,
              "value": 4
            },
            {
              "key": 21,
              "value": 5
            },
            {
              "key": 22,
              "value": 6
            },
            {
              "key": 23,
              "value": 7
            },
            {
              "key": 24,
              "value": 8
            },
            {
              "key": 25,
              "value": 9
            },
            {
              "key": 26,
              "value": 10
            },
            {
              "key": 27,
              "value": 11
            },
            {
              "key": 28,
              "value": 12
            },
            {
              "key": 29,
              "value": 13
            },
            {
              "key": 30,
              "value": 14
            },
            {
              "key": 31,
              "value": 15
            }
          ],
          "outputAddrTimePatterns": [
            {
              "value": 1
            },
            {
              "key": 1,
              "value": 2
            },
            {
              "key": 2,
              "value": 3
            },
            {
              "key": 3,
              "value": 4
            },
            {
              "key": 4,
              "value": 5
            },
            {
              "key": 5,
              "value": 6
            },
            {
              "key": 6,
              "value": 7
            },
            {
              "key": 7,
              "value": 8
            },
            {
              "key": 8,
              "value": 9
            },
            {
              "key": 9,
              "value": 10
            },
            {
              "key": 10,
              "value": 11
            },
            {
              "key": 11,
              "value": 12
            },
            {
              "key": 12,
              "value": 13
            },
            {
              "key": 13,
              "value": 14
            },
            {
              "key": 14,
              "value": 15
            },
            {
              "key": 15,
              "value": 16
            }
          ]
        }
      ]
    },
    {
      "func": "func_store",
      "instances": [
        {
          "width": 4,
          "height": 2,
          "latency": 5,
          "energy": 1,
          "inputAddrTimePatterns": [
            {},
            {
              "key": 1
            },
            {
              "key": 2
            },
            {
              "key": 3
            },
            {
              "key": 4,
              "value": 1
            },
            {
              "key": 5,
              "value": 1
            },
            {
              "key": 6,
              "value": 1
            },
            {
              "key": 7,
              "value": 1
            },
            {
              "key": 8,
              "value": 2
            },
            {
              "key": 9,
              "value": 2
            },
            {
              "key": 10,
              "value": 2
            },
            {
              "key": 11,
              "value": 2
            },
            {
              "key": 12,
              "value": 3
            },
            {
              "key": 13,
              "value": 3
            },
            {
              "key": 14,
              "value": 3
            },
            {
              "key": 15,
              "value": 3
            }
          ]
        }
      ]
    }
  ]
}
```

## Hyper Parameters

The hyper parameters are the parameters that are used to tune the DSE tools. The hyper parameters are represented in the form of a JSON file. See the following example. The hyper parameter file is evolving and the parameters may change in the future.

```json
{
  "bindWArea": 1,
  "bindWEnergy": 1,
  "bindWLatency": 1,
  "bindRelaxationFactor": 1.1,
  "placeRelaxationFactor": 1.0,
  "placeReservedRoutingSize": 1
}
```

## Global Constraints

The global constraints are the constraints that are used to limit the search space of the DSE tools. The global constraints are represented in the form of a JSON file. See the following example.

```json
{
  "maxWidth": 100,
  "maxHeight": 100,
  "maxLatency": 100,
  "maxPeriod": 70,
  "maxEnergy": 100
}
```

## Technology Constraints

The technology constraints are the physical constraints from the selected technology that the application are targeted to. The technology constraints are represented in the form of a JSON file. See the following example. The technology constraints file is evolving and the parameters may change in the future.

```json
{
  "required_period": 4000,
  "required_slew": 40,
  "initial_slew": 70,
  "buffer_slew_declined_factor": 5,
  "buffer_delay_improved_factor": 600,
  "register_slew_constant": 100,
  "number_slew_rates": 30,
  "number_wire_blocks": 30,
  "slew_rates": [40, 45, 50, 55, 60, 65, 70, 75, 80, 85, 90, 95, 100, 105, 110, 115, 120, 125, 130, 135, 140, 145, 150, 155, 160, 165, 170, 175, 180, 185],
  "timing_table": [
    { "rows": [1000, 1300, 1600, 1900, 2200, 2500, 2800, 3100, 3400, 3700, 4000, 4300, 4600, 4900, 5200, 5500, 5800, 6100, 6400, 6700, 7000, 7300, 7600, 7900, 8200, 8500, 8800, 9100, 9400, 9700] },
    { "rows": [970, 1270, 1570, 1870, 2170, 2470, 2770, 3070, 3370, 3670, 3970, 4270, 4570, 4870, 5170, 5470, 5770, 6070, 6370, 6670, 6970, 7270, 7570, 7870, 8170, 8470, 8770, 9070, 9370, 9670] },
    { "rows": [940, 1240, 1540, 1840, 2140, 2440, 2740, 3040, 3340, 3640, 3940, 4240, 4540, 4840, 5140, 5440, 5740, 6040, 6340, 6640, 6940, 7240, 7540, 7840, 8140, 8440, 8740, 9040, 9340, 9640] },
    { "rows": [910, 1210, 1510, 1810, 2110, 2410, 2710, 3010, 3310, 3610, 3910, 4210, 4510, 4810, 5110, 5410, 5710, 6010, 6310, 6610, 6910, 7210, 7510, 7810, 8110, 8410, 8710, 9010, 9310, 9610] },
    { "rows": [880, 1180, 1480, 1780, 2080, 2380, 2680, 2980, 3280, 3580, 3880, 4180, 4480, 4780, 5080, 5380, 5680, 5980, 6280, 6580, 6880, 7180, 7480, 7780, 8080, 8380, 8680, 8980, 9280, 9580] },
    { "rows": [850, 1150, 1450, 1750, 2050, 2350, 2650, 2950, 3250, 3550, 3850, 4150, 4450, 4750, 5050, 5350, 5650, 5950, 6250, 6550, 6850, 7150, 7450, 7750, 8050, 8350, 8650, 8950, 9250, 9550] },
    { "rows": [820, 1120, 1420, 1720, 2020, 2320, 2620, 2920, 3220, 3520, 3820, 4120, 4420, 4720, 5020, 5320, 5620, 5920, 6220, 6520, 6820, 7120, 7420, 7720, 8020, 8320, 8620, 8920, 9220, 9520] },
    { "rows": [790, 1090, 1390, 1690, 1990, 2290, 2590, 2890, 3190, 3490, 3790, 4090, 4390, 4690, 4990, 5290, 5590, 5890, 6190, 6490, 6790, 7090, 7390, 7690, 7990, 8290, 8590, 8890, 9190, 9490] },
    { "rows": [760, 1060, 1360, 1660, 1960, 2260, 2560, 2860, 3160, 3460, 3760, 4060, 4360, 4660, 4960, 5260, 5560, 5860, 6160, 6460, 6760, 7060, 7360, 7660, 7960, 8260, 8560, 8860, 9160, 9460] },
    { "rows": [730, 1030, 1330, 1630, 1930, 2230, 2530, 2830, 3130, 3430, 3730, 4030, 4330, 4630, 4930, 5230, 5530, 5830, 6130, 6430, 6730, 7030, 7330, 7630, 7930, 8230, 8530, 8830, 9130, 9430] },
    { "rows": [700, 1000, 1300, 1600, 1900, 2200, 2500, 2800, 3100, 3400, 3700, 4000, 4300, 4600, 4900, 5200, 5500, 5800, 6100, 6400, 6700, 7000, 7300, 7600, 7900, 8200, 8500, 8800, 9100, 9400] },
    { "rows": [670, 970, 1270, 1570, 1870, 2170, 2470, 2770, 3070, 3370, 3670, 3970, 4270, 4570, 4870, 5170, 5470, 5770, 6070, 6370, 6670, 6970, 7270, 7570, 7870, 8170, 8470, 8770, 9070, 9370] },
    { "rows": [640, 940, 1240, 1540, 1840, 2140, 2440, 2740, 3040, 3340, 3640, 3940, 4240, 4540, 4840, 5140, 5440, 5740, 6040, 6340, 6640, 6940, 7240, 7540, 7840, 8140, 8440, 8740, 9040, 9340] },
    { "rows": [610, 910, 1210, 1510, 1810, 2110, 2410, 2710, 3010, 3310, 3610, 3910, 4210, 4510, 4810, 5110, 5410, 5710, 6010, 6310, 6610, 6910, 7210, 7510, 7810, 8110, 8410, 8710, 9010, 9310] },
    { "rows": [580, 880, 1180, 1480, 1780, 2080, 2380, 2680, 2980, 3280, 3580, 3880, 4180, 4480, 4780, 5080, 5380, 5680, 5980, 6280, 6580, 6880, 7180, 7480, 7780, 8080, 8380, 8680, 8980, 9280] },
    { "rows": [550, 850, 1150, 1450, 1750, 2050, 2350, 2650, 2950, 3250, 3550, 3850, 4150, 4450, 4750, 5050, 5350, 5650, 5950, 6250, 6550, 6850, 7150, 7450, 7750, 8050, 8350, 8650, 8950, 9250] },
    { "rows": [520, 820, 1120, 1420, 1720, 2020, 2320, 2620, 2920, 3220, 3520, 3820, 4120, 4420, 4720, 5020, 5320, 5620, 5920, 6220, 6520, 6820, 7120, 7420, 7720, 8020, 8320, 8620, 8920, 9220] },
    { "rows": [490, 790, 1090, 1390, 1690, 1990, 2290, 2590, 2890, 3190, 3490, 3790, 4090, 4390, 4690, 4990, 5290, 5590, 5890, 6190, 6490, 6790, 7090, 7390, 7690, 7990, 8290, 8590, 8890, 9190] },
    { "rows": [460, 760, 1060, 1360, 1660, 1960, 2260, 2560, 2860, 3160, 3460, 3760, 4060, 4360, 4660, 4960, 5260, 5560, 5860, 6160, 6460, 6760, 7060, 7360, 7660, 7960, 8260, 8560, 8860, 9160] },
    { "rows": [430, 730, 1030, 1330, 1630, 1930, 2230, 2530, 2830, 3130, 3430, 3730, 4030, 4330, 4630, 4930, 5230, 5530, 5830, 6130, 6430, 6730, 7030, 7330, 7630, 7930, 8230, 8530, 8830, 9130] },
    { "rows": [400, 700, 1000, 1300, 1600, 1900, 2200, 2500, 2800, 3100, 3400, 3700, 4000, 4300, 4600, 4900, 5200, 5500, 5800, 6100, 6400, 6700, 7000, 7300, 7600, 7900, 8200, 8500, 8800, 9100] },
    { "rows": [370, 670, 970, 1270, 1570, 1870, 2170, 2470, 2770, 3070, 3370, 3670, 3970, 4270, 4570, 4870, 5170, 5470, 5770, 6070, 6370, 6670, 6970, 7270, 7570, 7870, 8170, 8470, 8770, 9070] },
    { "rows": [340, 640, 940, 1240, 1540, 1840, 2140, 2440, 2740, 3040, 3340, 3640, 3940, 4240, 4540, 4840, 5140, 5440, 5740, 6040, 6340, 6640, 6940, 7240, 7540, 7840, 8140, 8440, 8740, 9040] },
    { "rows": [310, 610, 910, 1210, 1510, 1810, 2110, 2410, 2710, 3010, 3310, 3610, 3910, 4210, 4510, 4810, 5110, 5410, 5710, 6010, 6310, 6610, 6910, 7210, 7510, 7810, 8110, 8410, 8710, 9010] },
    { "rows": [280, 580, 880, 1180, 1480, 1780, 2080, 2380, 2680, 2980, 3280, 3580, 3880, 4180, 4480, 4780, 5080, 5380, 5680, 5980, 6280, 6580, 6880, 7180, 7480, 7780, 8080, 8380, 8680, 8980] },
    { "rows": [250, 550, 850, 1150, 1450, 1750, 2050, 2350, 2650, 2950, 3250, 3550, 3850, 4150, 4450, 4750, 5050, 5350, 5650, 5950, 6250, 6550, 6850, 7150, 7450, 7750, 8050, 8350, 8650, 8950] },
    { "rows": [220, 520, 820, 1120, 1420, 1720, 2020, 2320, 2620, 2920, 3220, 3520, 3820, 4120, 4420, 4720, 5020, 5320, 5620, 5920, 6220, 6520, 6820, 7120, 7420, 7720, 8020, 8320, 8620, 8920] },
    { "rows": [190, 490, 790, 1090, 1390, 1690, 1990, 2290, 2590, 2890, 3190, 3490, 3790, 4090, 4390, 4690, 4990, 5290, 5590, 5890, 6190, 6490, 6790, 7090, 7390, 7690, 7990, 8290, 8590, 8890] },
    { "rows": [160, 460, 760, 1060, 1360, 1660, 1960, 2260, 2560, 2860, 3160, 3460, 3760, 4060, 4360, 4660, 4960, 5260, 5560, 5860, 6160, 6460, 6760, 7060, 7360, 7660, 7960, 8260, 8560, 8860] },
    { "rows": [130, 430, 730, 1030, 1330, 1630, 1930, 2230, 2530, 2830, 3130, 3430, 3730, 4030, 4330, 4630, 4930, 5230, 5530, 5830, 6130, 6430, 6730, 7030, 7330, 7630, 7930, 8230, 8530, 8830] }
  ]
}
```
