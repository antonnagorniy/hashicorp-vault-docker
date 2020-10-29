Разворачивание:

    git clone https://github.com/laspavel/hashicorp-vault-docker.git
    cd hashicorp-vault-docker
    # Выставляем необходимые версии Vault and Consul в аргументах сборки (Параметры: VAULT_VERSION, CONSUL_VERSION)
    nano docker-compose.yml
    # Собираемся и запускаемся
    docker-compose up --build -d

Если нужен прокси для выхода в Интернет создаем:

    mkdir ~./docker
    nano config.json

и там указываем параметры прокси:

    {
     "proxies":
      {
         "default":
            {
                 "httpProxy": "http://myproxy:8888",
                 "httpsProxy": "http://myproxy:8888",
                 "noProxy": "*.localsites.com,.localsites.com,consul"
            }
      }
    }


## Включение AD авторизации.

    vault login token=<TOKEN>
    vault auth enable ldap
    vault write auth/ldap/config \
        url="ldap://example.com" \
        userattr=sAMAccountName \
        upndomain="example.com" \
        userdn="ou=Users,dc=example,dc=com" \
        groupdn="ou=Users,dc=example,dc=com" \
        groupfilter="(|(memberUid={{.Username}})(member={{.UserDN}})(uniqueMember={{.UserDN}}))" \
        groupattr="memberOf" \
        binddn="cn=vault,ou=users,dc=example,dc=com" \
        bindpass='My$ecrt3tP4ss'

**example:**

       vault write auth/ldap/config \
            url="ldap://mycdserver.com" \
            userattr=sAMAccountName \
            upndomain="mydomain.com" \
            userdn="dc=mydomain,dc=com" \
            groupfilter="(|(memberUid={{.Username}})(member={{.UserDN}})(uniqueMember={{.UserDN}}))" \
            groupattr="cn" \
            binddn="CN=my_ldap_search,OU=myGroup,OU=Service-Accounts,DC=mydomain,DC=com" \
            bindpass='Passss'

## Включение AUDIT:

    vault login token=<TOKEN>
    vault audit enable file file_path=/vault/logs/vault_audit.log

Документация: https://www.vaultproject.io/api-docs/index/
