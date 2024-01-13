# tesla_fleet_api
Python library for Tesla Fleet API and Teslemetry.

Currently does not support the end to end encrypted telemetry or command API.

Based on [Tesla Developer documentation](https://developer.tesla.com/docs/fleet-api).


## TeslaFleetApi
This is the base class, however can also be used directly if you have a valid user access_token.

```
import asyncio
import aiohttp

from tesla_fleet_api import TeslaFleetApi, TeslaFleetError


async def main():
    async with aiohttp.ClientSession() as session:
        api = TeslaFleetApi(
            access_token="<access_token>",
            session=session,
            region="na",
            raise_for_status=True,
        )

        try:
            data = await api.vehicle.list()
            print(data)
        except TeslaFleetError.Base as e:
            print(e.message, e.error)

asyncio.run(main())
```

## TeslaFleetOAuth
This extends TeslaFleetApi to support OAuth, and requires a client_id, and either a refresh_token or initial authentication code.

```
import json

async def main():
    with open("auth.json", "r") as f:
        auth = json.load(f)
    async with aiohttp.ClientSession() as session:
        api = TeslaFleetOAuth(
            session,
            client_id=<client_id>,
            access_token=auth["access_token"],
            refresh_token=auth["refresh_token"],
            expires=auth["expires"],
            region="na",
            raise_for_status=True,
        )
        try:
            data = await api.vehicle.list()
            print(data)
        except TeslaFleetError.Base as e:
            print(e.message, e.error)

    with open("auth.json", "w") as f:
        json.dump(
            {
                "access_token": api.access_token,
                "refresh_token": api.refresh_token,
                "expires": api.expires,
            },
            f,
        )

asyncio.run(main())
```

## Teslemetry
This extends TeslaFleetApi to send requests through Teslemetry, which manages all aspects of Tesla OAuth. This class only requires an access_token from the Teslemetry console.

```
import asyncio
import aiohttp

from tesla_fleet_api import Teslemetry
from tesla_fleet_api.exceptions import TeslaFleetError


async def main():
    async with aiohttp.ClientSession() as session:
        api = Teslemetry(
            access_token="<access_token>",
            session=session,
            raise_for_status=True,
        )

        try:
            data = await api.vehicle.list()
            print(data)
        except TeslaFleetError as e:
            print(e.message, e.error)

asyncio.run(main())
```