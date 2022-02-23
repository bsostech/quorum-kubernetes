
install-genesis:
	helm install genesis ./charts/goquorum-genesis --namespace quorum --create-namespace --values ./values/genesis-goquorum.yml

install-validators:
	helm install validator-1 ./charts/goquorum-node --namespace quorum --values ./values/validator.yml && \
	helm install validator-2 ./charts/goquorum-node --namespace quorum --values ./values/validator.yml && \
	helm install validator-3 ./charts/goquorum-node --namespace quorum --values ./values/validator.yml && \
	helm install validator-4 ./charts/goquorum-node --namespace quorum --values ./values/validator.yml

install-ingress:
	helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx && \
	helm repo update && \
	helm install quorum-ingress ingress-nginx/ingress-nginx \
		--namespace quorum \
		--set controller.replicaCount=1 \
		--set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
		--set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
		--set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux \
		--set controller.service.externalTrafficPolicy=Cluster && \
	kubectl apply -f ../../ingress/ingress-rules-quorum.yml

# spin up a quorum and tessera node pair
install-txnode:
	helm install member-1 ./charts/goquorum-node --namespace quorum --values ./values/txnode.yml

uninstall-genesis:
	helm uninstall genesis -n quorum

uninstall-validators:
	helm uninstall validator-1 -n quorum && \
	helm uninstall validator-2 -n quorum && \
	helm uninstall validator-3 -n quorum && \
	helm uninstall validator-4 -n quorum

uninstall-txnode:
	helm uninstall member-1 -n quorum

uninstall-ingress:
	helm uninstall quorum-ingress -n quorum

INGRESS_IP = 104.199.180.194

# HTTP RPC API:
test-rpc:
	curl -v -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"eth_blockNumber","params":[],"id":1}' http://${INGRESS_IP}/validator-1
	# curl -v -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"eth_blockNumber","params":[],"id":1}' http://${INGRESS_IP}/rpc
	# which should return (confirming that the node running the JSON-RPC service is syncing):
	# {
	#   "jsonrpc" : "2.0",
	#   "id" : 1,
	#   "result" : "0x4e9"
	# }

# HTTP GRAPHQL API:
test-graphql:
	curl -X POST -H "Content-Type: application/json" --data '{ "query": "{syncing{startingBlock currentBlock highestBlock}}"}' http://${INGRESS_IP}/graphql/
	# which should return
	# {
	#   "data" : {
	#     "syncing" : null
	#   }
	# }
