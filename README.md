# Property Owner Records MCP Server

[![npm version](https://img.shields.io/npm/v/@mambalabsdev/mcp-property-owner-records)](https://www.npmjs.com/package/@mambalabsdev/mcp-property-owner-records) [![license](https://img.shields.io/github/license/mambalabsdev/mcp-property-owner-records)](https://github.com/mambalabsdev/mcp-property-owner-records/blob/main/LICENSE)

An MCP server for the Mamba Labs Property Owner Records actor on Apify. Addresses or parcels in, owners of record from county tax rolls out.

## What's Inside

- [What it does](#what-it-does)
- [Quick start](#quick-start)
- [Prerequisites](#prerequisites)
- [Example prompts](#example-prompts)
- [Tool](#tool)
- [Inputs](#inputs)
- [Pricing](#pricing)
- [Full actor documentation](#full-actor-documentation)
- [License](#license)

## What it does

Give US street addresses, or a state with parcel IDs, owner names, or ZIP codes. The actor reads six public statewide parcel layers built from county assessor rolls (NC, NY in 38 counties, FL, CO, AR, WI) and returns one row per parcel with the owner of record, the mailing address on the roll, the property address, assessed and market values, land use, and last sale where the county publishes it. It returns no phone numbers or emails and uses no data broker.

All of the work runs on Apify. This package is a thin client that starts the actor run, waits for it, and hands back the dataset rows.

## Quick start

You need Node.js 18 or newer and an Apify account with an API token.

Add this to your Claude Desktop config (`claude_desktop_config.json`), Cursor, or any MCP client:

```json
{
  "mcpServers": {
    "mamba-property-owner-records": {
      "command": "npx",
      "args": [
        "-y",
        "@mambalabsdev/mcp-property-owner-records"
      ],
      "env": {
        "APIFY_TOKEN": "your-apify-token"
      }
    }
  }
}
```

Restart the client. The `lookup_property_owner_records` tool appears.

## Prerequisites

- Node.js 18 or newer.
- An Apify API token from [console.apify.com/account/integrations](https://console.apify.com/account/integrations), set as `APIFY_TOKEN`. The server lists its tool without a token; a call needs one.

## Example prompts

- "Who owns 222 W Hargett St, Raleigh, NC 27601?"
- "List the parcels the City of Raleigh owns in Wake County, North Carolina."
- "Export the owners of record for every parcel in ZIP 27601."

## Tool

`lookup_property_owner_records`. One tool. Example arguments:

```json
{
  "addresses": [
    "1 E Edenton St, Raleigh, NC 27601"
  ],
  "owner_names": [
    "CITY OF RALEIGH"
  ],
  "state": "NC",
  "county": "Wake",
  "max_results_per_lookup": 5
}
```

## Inputs

Every buyer facing input of the actor. Omitted inputs take the actor's own defaults.

| Input | Type | Required | Description |
|---|---|---|---|
| `addresses` | array | no | Full US street addresses with city and state, one per entry, such as 1 E Edenton St, Raleigh, NC 27601. The US Census geocoder places each one and the actor returns the parcel at that point. The state comes from the address, so the state field is not needed. An address it cannot place, or one outside the covered states, is a free row. |
| `parcel_ids` | array | no | Parcel numbers as the county writes them. Needs state. Add county, because the same number can repeat in two counties. |
| `owner_names` | array | no | Owner names as the roll writes them, usually last name first for a person (SMITH JOHN) and the full name for a company (CITY OF RALEIGH). Needs state. Matches names that start with the text unless owner_match is contains. |
| `zip_codes` | array | no | 5 digit property ZIP codes. Returns every parcel in the ZIP up to max_results_per_lookup. Needs state. |
| `state` | string | no | Two letter state for parcel_ids, owner_names, and zip_codes: NC, NY, FL, CO, AR, or WI. |
| `county` | string | no | Optional county name, such as Wake or St. Lawrence, to narrow parcel_ids, owner_names, and zip_codes. Does not apply to addresses. |
| `owner_match` | string | no | starts_with (default): the owner name starts with your text. contains: the text appears anywhere in the name; slower, so add a county. |
| `max_results_per_lookup` | number | no | Parcel rows per address, parcel ID, owner name, or ZIP, from 1 to 5000. Default 25. |
| `max_charge_usd` | string | no | Optional. The run stops before a charge that would pass this amount. Summary rows are always returned. |

## Pricing

Pay per event on Apify, billed to your Apify account. Prices in USD.

| Event | Free plan | Bronze | Silver | Gold |
|---|---|---|---|---|
| `parcel-row`, one parcel record | $0.02 | $0.019 | $0.018 | $0.017 |
| `apify-actor-start`, per run start | $0.009 | $0.009 | $0.009 | $0.009 |

Lookup summaries, misses, refused requests, and refused input are free. Mamba Labs pays the platform costs of the run. Mamba Labs pays the platform costs of the run, Apify proxy traffic included.

## Full actor documentation

[apify.com/mambalabs/property-owner-records](https://apify.com/mambalabs/property-owner-records)

## License

MIT. See [LICENSE](LICENSE).
