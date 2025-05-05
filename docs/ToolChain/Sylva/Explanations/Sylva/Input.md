# Input

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
