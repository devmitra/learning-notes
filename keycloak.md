# Keycloak

## Add default role to access token

Date: 6-Jul-2021

- Using **Mapper** > Mapper type **Hardcoded Role**
- Using **configure** > **Roles** > **Default Roles** > select client and default client role

## Export user prop or attribute to access token

- User **Mapper** > Mapper type **User Attribute**
- Use **configure** > **Client Scope** > Create new scope | In new scope add mapper with **User Attribute** or **User Property** type

## Extending Keycloak Server

Date: 6-Jul-2021

[Server Developer Ref](https://www.keycloak.org/docs/latest/server_development/#preface)

[Retrieve external token](https://www.keycloak.org/docs/latest/server_development/#preface)

### Add `read-token` to access token

### Case 1

- Add role mapping to user > client: **broker** , role: **read-token**
- Add **roles** scope to authenticating client

#### Case 2

- For authenticating (application) client goto Mapper > Create
- Mapper type **Hardcoded Role**
- Select client id **broker**
- Put `client role prefix` => **read-token**
- Enable add to **access token**
- Add **roles** scope to authenticating client
