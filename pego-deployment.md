# Suitcase on Pego Network
This project is a fork from Gnosis Safe Global. The Suitcase project consists of the following 8 repositories. To deploy the project, go to each repo by following the sequence below and refer to steps stated in `pego-deployment.md`, which can be found in each repo.

1. [safe-singleton-factory](https://github.com/Pego-DAO/safe-singleton-factory)
2. [suitcase-contract](https://github.com/Pego-DAO/suitcase-contract)
3. [safe-transaction-service](https://github.com/Pego-DAO/safe-transaction-service)
4. [safe-client-gateway](https://github.com/Pego-DAO/safe-client-gateway)
5. **[safe-config-service](https://github.com/Pego-DAO/safe-config-service)** - Currently viewing
6. [suitcase-deployment](https://github.com/Pego-DAO/suitcase-deployment)
7. [suitcase-sdk](https://github.com/Pego-DAO/suitcase-sdk)
8. [suitcase-ui](https://github.com/Pego-DAO/suitcase-ui)

## safe-config-service
*Prerequisite: Smart contract in [suitcase-contract](https://github.com/Pego-DAO/suitcase-contract) have deployed successfully to the target network*

1. Create a `.env` file and copy the following code and provide value accordingly.
```
PYTHONDONTWRITEBYTECODE=true
SECRET_KEY=lrlIvEVRxHimueOmPk2jUExxxxxx # Random string, follow 'DJANGO_SECRET_KEY' on .env file of safe-transaction-service
GUNICORN_BIND_PORT=8000
DOCKER_NGINX_VOLUME_ROOT=/nginx
GUNICORN_BIND_SOCKET=unix:/nginx/gunicorn.socket
NGINX_HOST_PORT=8080
NGINX_ENVSUBST_OUTPUT_DIR=/etc/nginx/
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres
POSTGRES_NAME=postgres
POSTGRES_HOST=localhost
POSTGRES_PORT=5432
GUNICORN_WEB_RELOAD=false
DJANGO_OTP_ADMIN=false
DEFAULT_FILE_STORAGE=django.core.files.storage.FileSystemStorage
CGW_URL=https://gateway.suitcase.plus # url of safe-client-gateway
CGW_FLUSH_TOKEN=QsOVcfYoOJYsuh1qcOAZyxxxxxxxxxxx # Random string, follow 'AUTH_TOKEN' in .env file of safe-client-gateway
DEBUG=false
ROOT_LOG_LEVEL=INFO
DOCKER_WEB_VOLUME=.:/app
MEDIA_URL=https://config.suitcase.plus/media/ # site url of safe-config-service + /media/
DJANGO_ALLOWED_HOSTS=*
CSRF_TRUSTED_ORIGINS=https://transaction.suitcase.plus,https://config.suitcase.plus,https://gateway.suitcase.plus,http://127.0.0.1:3333,http://127.0.0.1:3000 # url of safe transaction, gateway service, config service, http://127.0.0.1:3333 and http://127.0.0.1:3000
```

2. Run this repo with docker
```
# Bring up docker container
docker compose up -d
```

3. Create an account to login config service,
```
docker exec -it <web-docker-container-id> python src/manage.py createsuperuser
```

4. Login to config service admin site at `<site_url>/admin`, and provide value to the following menu,
    - `Wallets` at `<site_url>/admin/chains/wallet/`, add `detectedwallet`, `keystone`, `trezor`, `ledger`, `safeMobile`, `coinbase` and `walletConnect_v2`.
    - `Chains` at `<site_url>/admin/chains/chain/`, add the following,
        *Below value are example of Pego Mainnet.*
        - Chain Id = 20201022
        - Chain Name, EIP-3770 short name, Description = pego
        - L2 = checked
        - Rpc authentication, Safe apps rpc authentication, Public rpc authentication = No Authentication
        - Rpc uri, Safe apps rpc uri, Public rpc uri = https://pegorpc.com
        - Block explorer uri address template = <blockchain_explorer_url>/address/{{address}}
        - Block explorer uri tx hash template = <blockchain_explorer_url>/tx/{{txHash}}
        - Block explorer uri api template = <blockchain_explorer_url>/api?module={{module}}&action={{action}}&address={{address}}&apiKey={{apiKey}}
        - Currency name, Currency symbol = PG
        - Currency decimals = 18
        - Currency logo uri = upload token logo
        - Transaction service uri, Vpc transaction service uri = https://transaction.suitcase.plus # this is transaction service url
        - Feature = select CONTRACT_INTERACTION, DOMAIN_LOOKUP, RC721, SPENDING_LIMIT
        - Wallet = detectedwallet