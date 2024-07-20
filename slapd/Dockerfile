FROM docker.io/library/debian:10-slim
SHELL ["/bin/bash", "-o", "pipefail", "-c"]
RUN apt-get update \
    && echo "slapd slapd/root_password password king123" | debconf-set-selections \
    && echo "slapd slapd/root_password_again password king123" | debconf-set-selections \
    && echo "slapd slapd/internal/adminpw password king123" | debconf-set-selections \
    && echo "slapd slapd/internal/generated_adminpw password king123" | debconf-set-selections \
    && echo "slapd slapd/password2 password king123" | debconf-set-selections \
    && echo "slapd slapd/password1 password king123" | debconf-set-selections \
    && echo "slapd slapd/domain string acme.local" | debconf-set-selections \
    && echo "slapd shared/organization string Acme" | debconf-set-selections \
    && echo "slapd slapd/backend string HDB" | debconf-set-selections \
    && echo "slapd slapd/purge_database boolean true" | debconf-set-selections \
    && echo "slapd slapd/move_old_database boolean true" | debconf-set-selections \
    && echo "slapd slapd/allow_ldap_v2 boolean false" | debconf-set-selections \
    && echo "slapd slapd/no_configuration boolean false" | debconf-set-selections \
    && DEBIAN_FRONTEND=noninteractive apt-get install \
        --assume-yes \
        --no-install-recommends \
        ldap-utils \
        slapd \
    && rm --force --recursive /var/lib/apt/list/*

COPY configuration/*.ldif /tmp/
RUN mkdir -p /var/ldap/acme \
    && chown --recursive openldap /var/ldap \
    && /etc/init.d/slapd start \
    && ldapadd -H ldapi:/// -f /tmp/ou.ldif -x -D "cn=admin,dc=acme,dc=local" -w king123 -v \
    && ldapadd -H ldapi:/// -f /tmp/groups.ldif -x -D "cn=admin,dc=acme,dc=local" -w king123 -v \
    && ldapadd -H ldapi:/// -f /tmp/users.ldif -x -D "cn=admin,dc=acme,dc=local" -w king123 -v \
    && rm --verbose /tmp/*.ldif

EXPOSE 389

CMD ["slapd", "-h", "ldap:///", "-g", "openldap", "-u", "openldap", "-d", "256"]

