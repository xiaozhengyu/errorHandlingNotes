**运行环境：**Win10 + WSL2 + DockerDesktop + ElasticSearch:7.8.1

**问题说明：**启动ES时抛出异常

```
ERROR: [1] bootstrap checks failed
[1]: the default discovery settings are unsuitable for production use; at least one of [discovery.seed_hosts, discovery.seed_providers, cluster.initial_master_nodes] must be configured
```

**解决方案：**在启动命令中加入 -e "discovery.type=single-node"

```
docker run -itd -p 9200:9200 -p 9300:9300  -e "discovery.type=single-node" --name es elasticsearch:7.8.1
```

