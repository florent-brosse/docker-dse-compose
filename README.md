docker-compose -f docker-compose.yml -f docker-compose.opscenter.yml up -d --scale node=1

http://localhost:8888
Click Manage existing cluster.
In host name, enter seed_node.
Click Install agents manually. Note that the agent is already installed on the DSE image; no installation is required.

docker exec -it --user=root dockerdsecompose_opscenter_1 bash

docker logs dockerdsecompose_seed_node_1
docker logs dockerdsecompose_node_1
docker logs dockerdsecompose_opscenter_1

docker-compose -f docker-compose.yml -f docker-compose.opscenter.yml stop


