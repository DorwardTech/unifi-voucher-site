# UniFi Voucher Site

A small UniFi Voucher Site for simple voucher creation

[![Image Size](https://img.shields.io/docker/image-size/glenndehaan/unifi-voucher-site)](https://hub.docker.com/r/glenndehaan/unifi-voucher-site)

![Vouchers Overview - Desktop](https://github.com/glenndehaan/unifi-voucher-site/assets/7496187/2bc18cce-afb2-41e9-a34c-d923fab9f6e4)

## Structure

- Javascript
- ExpressJS
- Node UniFi
- TailwindCSS

## Development Usage

- Install NodeJS 20.0 or higher.
- Run `npm ci` in the root folder
- Run `npm start` & `npm run tailwind` in the root folder

Then open up your favorite browser and go to http://localhost:3000/

## Build Usage

- Install NodeJS 20.0 or higher.
- Run `npm ci` in the root folder
- Run `npm run build` in the root folder

## Docker

- Code from master is build by Docker Hub
- Builds can be pulled by using this command: `docker pull glenndehaan/unifi-voucher-site`
- An example docker compose file can be found below:

```yaml
version: '3'
services:
  app:
    image: glenndehaan/unifi-voucher-site
    ports:
      - "3000:3000"
    environment:
      # The IP address to your UniFi OS Console
      UNIFI_IP: '192.168.1.1'
      # The port of your UniFi OS Console, this could be 443 or 8443
      UNIFI_PORT: 443
      # The username of a local UniFi OS account
      UNIFI_USERNAME: 'admin'
      # The password of a local UniFi OS account
      UNIFI_PASSWORD: 'password'
      # The UniFi Site ID
      UNIFI_SITE_ID: 'default'
      # The 'password' used to log in to the voucher portal and used as Bearer token for the API
      SECURITY_CODE: '0000'
      # Disables the login/authentication for the portal and API
      DISABLE_AUTH: 'false'
      # Voucher Types, format: expiration in minutes (required),single-use or multi-use vouchers value - '0' is for multi-use - '1' is for single-use (optional),upload speed limit in kbps (optional),download speed limit in kbps (optional),data transfer limit in MB (optional)
      # To skip a parameter just but nothing in between the comma's
      # After a voucher type add a semicolon, after the semicolon you can start a new voucher type
      VOUCHER_TYPES: '480,1,,,;'
      # Allow users to create custom vouchers types within the UI
      VOUCHER_CUSTOM: 'true'
      # Enable/disable the Web UI
      SERVICE_WEB: 'true'
      # Enable/disable the API
      SERVICE_API: 'false'
      # Sets the application Log Level (Valid Options: error|warn|info|debug|trace)
      LOG_LEVEL: 'info'
```

> Attention!: We recommend only using Local UniFi accounts due to short token lengths provided by UniFi Cloud Accounts. Also UniFi Cloud Accounts using 2FA won't work!

## Services

The project consists of two main services: Web and API.

### Web Service

The Web service is a user interface accessible through a web browser. It provides functionality for generating, viewing,
and managing vouchers within the UniFi system.

### API Service

The API service allows programmatic access to voucher-related functionalities. It is designed for developers who wish to
integrate voucher management into their applications or automate voucher generation processes. Below are the details of
the different endpoints available in the API:

#### Endpoints

1. **`/api`**
    - Method: GET
    - Description: Retrieves information about the API and its endpoints.
    - Access: Open
    - Response Example:

```json
{
  "error": null,
  "data": {
    "message": "OK",
    "endpoints": [
      "/api",
      "/api/types",
      "/api/voucher/:type",
      "/api/vouchers"
    ]
  }
}
```

2. **`/api/types`**
    - Method: GET
    - Description: Retrieves a list of available voucher types supported by the system.
    - Response Format: JSON
    - Access: Open
    - Response Example:

```json
{
  "error": null,
  "data": {
    "message": "OK",
    "types": [
      {
        "expiration": "480",
        "usage": "0",
        "raw": "480,0,,,"
      }
    ]
  }
}
```

3. **`/api/voucher/:type`**
    - Method: GET
    - Description: Generates a voucher of the specified type.
    - Parameters:
        - `type` (string): The type of voucher to generate.
    - Response Format: JSON
    - Access: Protected by Bearer Token
    - Response Example:

```json
{
  "error": null,
  "data": {
    "message": "OK",
    "voucher": "12345-67890"
  }
}
```

   > This endpoint is protected by a security mechanism. To access it, users need to include a bearer token in the
   request authorization header. The token must match the value of the `SECURITY_CODE` environment variable. Without
   this token, access to the endpoint will be denied.

4. **`/api/vouchers`**
    - Method: GET
    - Description: Retrieves a list of available vouchers.
    - Response Format: JSON
    - Access: Protected by Bearer Token
    - Response Example:

```json
{
  "error": null,
  "data": {
    "message": "OK",
    "vouchers": [
      {
        "code": "15695-53133",
        "type": "multi",
        "duration": 60,
        "data_limit": "200",
        "download_limit": "5000",
        "upload_limit": "2000"
      },
      {
        "code": "03004-59449",
        "type": "single",
        "duration": 480,
        "data_limit": null,
        "download_limit": null,
        "upload_limit": null
      }
    ],
    "updated": 1712934667937
  }
}
```

> This endpoint is protected by a security mechanism. To access it, users need to include a bearer token in the
request authorization header. The token must match the value of the `SECURITY_CODE` environment variable. Without
this token, access to the endpoint will be denied.

## Home Assistant Add-on

For users of Home Assistant, we provide a dedicated add-on to seamlessly integrate the UniFi Voucher Site with your Home Assistant instance. This add-on simplifies the setup process and allows you to manage UniFi vouchers directly from your Home Assistant dashboard.

[![Open your Home Assistant instance and show the add add-on repository dialog with a specific repository URL pre-filled.](https://my.home-assistant.io/badges/supervisor_add_addon_repository.svg)](https://my.home-assistant.io/redirect/supervisor_add_addon_repository/?repository_url=https%3A%2F%2Fgithub.com%2Fglenndehaan%2Fha-addons)

### Installation

To install the UniFi Voucher Site add-on for Home Assistant, follow these steps:

1. Open the Supervisor panel in your Home Assistant instance.
2. Navigate to the "Add-on Store."
3. Add our repository to the list of repositories by clicking the three dots in the upper-right corner, then selecting "Repositories," and entering the URL of our repository: `https://github.com/glenndehaan/ha-addons`.
4. Once the repository is added, you will find the "UniFi Voucher Site" add-on in the add-on store. Click on it.
5. Click "Install" and wait for the installation to complete.

## Print Functionality

The UniFi Voucher Site application includes built-in support for printing vouchers using 80mm receipt printers, offering a convenient way to distribute vouchers in physical format.

### Compatibility

The print functionality is compatible with most 80mm thermal receipt printers commonly used in various industries. These printers typically use thermal printing technology, eliminating the need for ink cartridges and ensuring efficient and cost-effective voucher printing.

### Usage

Once your 80mm receipt printer is configured and connected, you can easily print vouchers directly from the UniFi Voucher Site application. Simply navigate to the voucher within the interface and click on the "Print" button.

The application will automatically format the voucher for 80mm paper width, ensuring optimal printing results. Depending on your printer settings and preferences, you may adjust print quality, paper type, and other printing parameters to suit your needs.

### Example Print PDF

![Example Print PDF](https://github.com/glenndehaan/unifi-voucher-site/assets/7496187/e86d0789-47d2-4630-a7fe-291a4fa9502f)

## Screenshots

### Login (Desktop)
![Login - Desktop](https://github.com/glenndehaan/unifi-voucher-site/assets/7496187/89289053-50e0-4169-9916-2bce7191bf49)

### Vouchers Overview (Desktop)
![Vouchers Overview - Desktop](https://github.com/glenndehaan/unifi-voucher-site/assets/7496187/2bc18cce-afb2-41e9-a34c-d923fab9f6e4)

### Create Voucher (Desktop)
![Create Voucher - Desktop](https://github.com/glenndehaan/unifi-voucher-site/assets/7496187/32803e88-c3cb-4708-914c-7e7184eae1c6)

### Login (Mobile)
![Login - Mobile](https://github.com/glenndehaan/unifi-voucher-site/assets/7496187/c236bb65-1ef5-41dd-976f-8f72a98416a3)

### Vouchers Overview (Mobile)
![Voucher Overview - Mobile](https://github.com/glenndehaan/unifi-voucher-site/assets/7496187/68656bad-b4a2-495d-baae-61a64ed52e72)

### Create Voucher (Mobile)
![Create Voucher - Mobile](https://github.com/glenndehaan/unifi-voucher-site/assets/7496187/b393c3d9-1e87-40e7-8998-9607872b161f)

## License

MIT
