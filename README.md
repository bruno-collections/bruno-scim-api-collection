# Bruno SCIM API Collection

A ready-to-run [Bruno](https://www.usebruno.com) collection for the Bruno License Server's SCIM 2.0 API. Use it to explore the API, test your SCIM API key, or manage users by hand before connecting an identity provider.

Full API reference: [Bruno SCIM API docs](https://docs.usebruno.com/license-administrators/scim-provisioning/bruno-scim-api)

## Get the collection

**Option 1: Fetch in Bruno**

[<img src="https://fetch.usebruno.com/button.svg" alt="Fetch in Bruno" style="width: 130px; height: 30px;" width="130" height="30">](https://fetch.usebruno.com?url=https://github.com/bruno-collections/bruno-scim-api-collection.git "target=_blank rel=noopener noreferrer")

**Option 2: Clone it**

```bash
git clone https://github.com/bruno-collections/bruno-scim-api-collection.git
```

Then in Bruno, choose **Open Collection** and select the `scim-collection` folder.

## Setup

1. Enable SCIM provisioning and generate a SCIM API key in your Bruno license portal. See [SCIM Provisioning](https://docs.usebruno.com/license-administrators/scim-provisioning/overview) for the steps.
2. In Bruno, select the **Production** environment.
3. Paste your key into the `scimApiKey` variable. It is marked as a secret, so it stays on your machine and is not written to the collection files.
4. Run **Service Provider Config** to confirm you can reach the server, then **List Users** to confirm your key works.

| Variable     | Default                        | Description                                         |
| ------------ | ------------------------------ | --------------------------------------------------- |
| `baseUrl`    | `https://license.usebruno.com` | License server URL. Change it if you self-host.     |
| `scimApiKey` | _(empty)_                      | Your SCIM API key, sent as a bearer token.          |
| `userId`     | _(set at runtime)_             | Set automatically when **Create User** succeeds.    |

## What's included

| Request                 | Method   | Endpoint                         | Notes                                                       |
| ----------------------- | -------- | -------------------------------- | ----------------------------------------------------------- |
| Service Provider Config | `GET`    | `/scim/v2/ServiceProviderConfig` | Public endpoint, no key needed. Lists supported features.   |
| List Users              | `GET`    | `/scim/v2/Users`                 | Paginated. Includes an optional `filter` param you can turn on. |
| Get User                | `GET`    | `/scim/v2/Users/:userId`         | Set the `userId` path param to the user you want.           |
| Create User             | `POST`   | `/scim/v2/Users`                 | Saves the new user's ID to `userId`.                        |
| Update User (PUT)       | `PUT`    | `/scim/v2/Users/{{userId}}`      | Replaces the whole user record.                             |
| Update User (PATCH)     | `PATCH`  | `/scim/v2/Users/{{userId}}`      | Changes only the fields you send.                           |
| Deactivate User         | `PATCH`  | `/scim/v2/Users/{{userId}}`      | Sets `active: false`.                                       |
| Delete User             | `DELETE` | `/scim/v2/Users/{{userId}}`      | Removes the user.                                           |

Each request has its own docs tab in Bruno with field details and examples.

## Typical workflow

1. **Create User** → the new user's ID is saved to `userId`.
2. **Update User (PATCH)** or **Update User (PUT)** to change details.
3. **Deactivate User** to suspend a user without deleting them.
4. **Delete User** to remove them.

The `userId` variable is reused by every request after step 1. To work with an existing user, find their ID with **List Users** and set `userId` yourself.

## Good to know

- **Authentication:** every request except Service Provider Config sends `Authorization: Bearer {{scimApiKey}}`.
- **Rate limits:** 100 requests per minute, with bursts of up to 20 requests per 10 seconds.
- **Pagination:** `count` goes up to 200. Increase `startIndex` by `count` until you have all `totalResults`.
- **Groups:** this collection covers Users only. Group endpoints aren't included yet.
- **Format:** the collection uses Bruno's YAML (OpenCollection) format. Use a recent version of Bruno to open it.

## Troubleshooting

| Response           | Likely cause                                                                           |
| ------------------ | -------------------------------------------------------------------------------------- |
| `401 Unauthorized` | `scimApiKey` is missing or wrong, or the **Production** environment isn't selected.     |
| `404 Not Found`    | `userId` points to a user that doesn't exist. Run **Create User** or check **List Users**. |
| `409 Conflict`     | A user with that `userName` already exists.                                            |
| Network error      | Your network blocks outbound HTTPS to `license.usebruno.com`. Check your firewall, proxy, or VPN. |

## License

[MIT](LICENSE)
