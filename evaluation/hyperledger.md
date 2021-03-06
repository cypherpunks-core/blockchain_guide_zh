## Hyperledger Fabric v0.6 性能評測

### 環境配置
| 類型  |     操作系統     | 內核版本 | CPU(GHz) | 內存(GB) |
| :--: | :-------------: | :-----: | :------: | :-----: |
| 物理機 | Ubuntu 14.04.1 | 3.16.0-71-generic | 4x2.0 | 8 |

每個集群啟動後等待 10s 以上，待狀態穩定。

僅測試單客戶端、單服務端的連接性能情況。

### 評測指標

一般評測系統性能指標包括吞吐量（throughput）和延遲（latency）。對於區塊鏈平臺系統來說，實際交易延遲包括客戶端到系統延遲（往往經過互聯網），再加上系統處理反饋延遲（跟不同 consensus 算法關係很大，跟集群之間互聯繫統關係也很大）。

本次測試僅給出大家最為關注的交易吞吐量（tps）。

### 結果

#### query 交易

##### noops
| clients | VP Nodes | iteration |   tps  |
| -------- | ------- | --------- | ------ |
|    1     |    1    |    2000   | 195.50 |
|    1     |    4    |    2000   | 187.09 |

##### pbft:classic
| clients | VP Nodes | iteration |   tps  |
| -------- | ------- | --------- | ------ |
|    1     |    4    |    2000   | 193.05 |

##### pbft:batch
| clients | VP Nodes | batch size | iteration |   tps  |
| -------- | ------- | --------  | ---------- | ------ |
|    1     |    4    |    2      |    2000    | 193.99 |
|    1     |    4    |    4      |    2000    | 192.49 |
|    1     |    4    |    8      |    2000    | 192.68 |

##### pbft:sieve
| clients | VP Nodes | iteration |   tps  |
| -------- | ------- | --------- | ------ |
|    1     |    4    |    2000   | 192.86 |

#### invoke 交易

##### noops

| clients | VP Nodes | iteration |   tps  |
| -------- | ------- | --------- | ------ |
|   1      |    1    |    2000   | 298.51 |
|   1      |    4    |    2000   | 205.76 |

##### pbft:classic
| clients | VP Nodes | iteration |  tps   |
| -------- | ------- | --------- | ------ |
|    1     |    4    |    2000   | 141.34 |


##### pbft:batch
| clients | VP Nodes | batch size | iteration |   tps  |
| -------- | ------- | ---------  | --------- | ------ |
|    1     |    4    |     2      |    2000   | 214.36 |
|    1     |    4    |     4      |    2000   | 227.53 |
|    1     |    4    |     8      |    2000   | 237.81 |


##### pbft:sieve
| clients | VP Nodes | iteration |   tps  |
| -------- | ------- | --------- | ------ |
|    1     |    4    |    2000   | 253.49* |

*注：sieve 算法目前在所有交易完成後較長時間內並沒有取得最終的結果，出現大量類似“vp0_1  | 07:49:26.388 [consensus/obcpbft] main -> WARN 23348 Sieve replica 0 custody expired, complaining: 3kwyMkdCSL4rbajn65v+iYWyJ5aqagXvRR9QU8qezpAZXY4y6uy2MB31SGaAiaSyPMM77TYADdBmAaZveM38zA==”警告信息。*

### 結論
單客戶端連接情況下，tps 基本在 190 ~ 300 範圍內。