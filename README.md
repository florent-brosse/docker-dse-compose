docker-compose -f docker-compose.yml -f docker-compose.opscenter.yml up -d --scale node=1

http://localhost:8888
Click Manage existing cluster.
In host name, enter seed_node.
Click Install agents manually. Note that the agent is already installed on the DSE image; no installation is required.
