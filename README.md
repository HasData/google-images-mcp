# Google Images MCP Server

<!-- mcp-name: com.hasdata/google-images -->

A hosted Model Context Protocol (MCP) server that gives Claude, Cursor, Windsurf and any other MCP client one read-only Google Images tool. Run an image search with size, colour and type filters and get 100 results a call, each with the page it came from and the full-resolution file URL, as structured JSON, with nothing to host.

It reads the Google Images results page a signed-out visitor sees.

**1,000 free credits every month, no card required**, which is 200 Google Images calls at the 5-credit rate.

```
https://mcp.hasdata.com/api/mcp?apis=google_images
```

[![Glama score](https://glama.ai/mcp/servers/HasData/google-images-mcp/badges/score.svg)](https://glama.ai/mcp/servers/HasData/google-images-mcp)
[![tool contract](https://github.com/HasData/google-images-mcp/actions/workflows/contract.yml/badge.svg)](https://github.com/HasData/google-images-mcp/actions/workflows/contract.yml)
[![MCP](https://img.shields.io/badge/MCP-remote%20%7C%20streamable%20HTTP-6366f1?style=flat-square)](https://mcp.hasdata.com/api/mcp?apis=google_images)
[![Tools](https://img.shields.io/badge/tools-1-10b981?style=flat-square)](#tools)
[![npm](https://img.shields.io/npm/v/@hasdata/google-images-mcp?style=flat-square&logo=npm&label=npm&color=cb3837)](https://www.npmjs.com/package/@hasdata/google-images-mcp)
[![PyPI](https://img.shields.io/pypi/v/hasdata-google-images-mcp?style=flat-square&logo=pypi&logoColor=white&label=PyPI&color=3775a9)](https://pypi.org/project/hasdata-google-images-mcp/)
[![License](https://img.shields.io/badge/license-MIT-blue?style=flat-square)](LICENSE)

## Contents

- [What you need](#what-you-need)
- [Quick start](#quick-start)
- [Example prompts](#example-prompts)
- [Tools](#tools)
- [Errors and failure paths](#errors-and-failure-paths)
- [Pricing, free tier and limits](#pricing-free-tier-and-limits)
- [How it compares](#how-it-compares)
- [FAQ](#faq)
- [HasData links](#hasdata-links)
- [Development](#development)
- [Contributing](#contributing)
- [License](#license)

## What you need

An MCP client and a HasData API key from the [dashboard](https://app.hasdata.com/sign-up?utm_source=github&utm_medium=syndication&utm_campaign=google-images-mcp), free to create with no card, and the free tier covers about 200 calls a month at the 5-credit rate. This is a remote server, so the simplest path is a URL and an `x-api-key` header, with no container to run. A client that only speaks stdio reaches it through a thin launcher, published as `@hasdata/google-images-mcp` on npm and `hasdata-google-images-mcp` on PyPI, shown below.

## Quick start

The server URL is the same for every client. We run it hands-on in Claude Code and Claude Desktop. The other blocks follow each client's own documented format for a remote server.

| Field | Value |
| :--- | :--- |
| URL | `https://mcp.hasdata.com/api/mcp?apis=google_images` |
| Transport | HTTP, streamable |
| Auth header | `x-api-key: HASDATA_API_KEY` |

Clients with OAuth support can add the same URL as a connector and sign in without putting a key in a config file.

<details>
<summary><b>Claude Code</b></summary>

```bash
claude mcp add --transport http google-images "https://mcp.hasdata.com/api/mcp?apis=google_images" \
  --header "x-api-key: HASDATA_API_KEY"
```

</details>

<details>
<summary><b>Claude Desktop</b></summary>

Settings, then Connectors, then Add custom connector, then paste `https://mcp.hasdata.com/api/mcp?apis=google_images` and sign in.

For the config-file route, Claude Desktop loads only local (stdio) servers, so it reaches a remote server through a stdio launcher. The `@hasdata/google-images-mcp` package is that launcher, and it reads the key from the environment. Add this to `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "google-images": {
      "command": "npx",
      "args": ["-y", "@hasdata/google-images-mcp"],
      "env": { "HASDATA_API_KEY": "YOUR_KEY" }
    }
  }
}
```

For Python instead of Node, swap the launcher for the PyPI package, which `uvx` runs without a manual install:

```json
{
  "mcpServers": {
    "google-images": {
      "command": "uvx",
      "args": ["hasdata-google-images-mcp"],
      "env": { "HASDATA_API_KEY": "YOUR_KEY" }
    }
  }
}
```

</details>

<details>
<summary><b>Cursor</b></summary>

`~/.cursor/mcp.json` for every project, or `.cursor/mcp.json` for one:

```json
{
  "mcpServers": {
    "google-images": {
      "url": "https://mcp.hasdata.com/api/mcp?apis=google_images",
      "headers": { "x-api-key": "HASDATA_API_KEY" }
    }
  }
}
```

</details>

<details>
<summary><b>Windsurf</b></summary>

`~/.codeium/windsurf/mcp_config.json`. Windsurf calls the field `serverUrl`, not `url`:

```json
{
  "mcpServers": {
    "google-images": {
      "serverUrl": "https://mcp.hasdata.com/api/mcp?apis=google_images",
      "headers": { "x-api-key": "HASDATA_API_KEY" }
    }
  }
}
```

</details>

<details>
<summary><b>VS Code</b></summary>

`.vscode/mcp.json` in the workspace:

```json
{
  "servers": {
    "google-images": {
      "type": "http",
      "url": "https://mcp.hasdata.com/api/mcp?apis=google_images",
      "headers": { "x-api-key": "HASDATA_API_KEY" }
    }
  }
}
```

</details>

## Example prompts

- Find images of the Austin skyline and give me the ones over 1500 pixels wide.
- Which sites are ranking images for this product name?
- Get black and white photographs for this query, skipping clipart and line art.
- Search this query from the German Google and compare the sources with the US ones.
- Find images of this landmark and tell me which are stock photos.
- Pull the next page of results for this query.

One call answers each of these, because a page carries 100 results. Paging is only needed past that.

## Tools

| Tool | What it returns |
| --- | --- |
| `hasdata_google_images_images_getImageSearchResults` | Each image with title, source page URL, direct image URL, thumbnail, dimensions, source domain, and position. 5 credits a call |

One tool, 5 credits per successful call.

### Get image search results

[`hasdata_google_images_images_getImageSearchResults`](https://docs.hasdata.com/apis/google-images/images?utm_source=github&utm_medium=syndication&utm_campaign=google-images-mcp)

One page of Google Images results.

| Parameter | Type | Required | Notes |
| :--- | :--- | :--- | :--- |
| `q` | string | yes | The search term |
| `tbs` | string | | Google's filter string for size, colour and type. See below |
| `location` | string | | Google canonical location for the search |
| `uule` | string | | The encoded location, if you already have one |
| `domain` | string | | Google domain, one of 195, defaults to `google.com` |
| `gl` | string | | Country code, one of 245 |
| `hl` | string | | Interface language, one of 159 |
| `safe` | string | | `active` or `off` |
| `filter` | number | | `1` keeps Google's similar and omitted result filters, `0` drops them |
| `deviceType` | string | | `desktop`, `mobile` or `tablet` |
| `ijn` | number | | Page number, where `0` is the first page |

Returns an `imagesResults` array. Each entry carries `position`, `title`, `link` to the page hosting the image, `source` as the site name, `thumbnail` on Google's CDN, `original` as the full-resolution file, and `originalWidth` and `originalHeight`.

```json
{
  "position": 1,
  "title": "The city skyline of Austin, Texas - Backyard Image",
  "link": "https://www.backyardimage.com/the-city-skyline-of-austin-texas/",
  "source": "Backyard Image",
  "thumbnail": "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcSOGtSVdgWtQtW1_x8-gi7iQckNHXBQ5FdKqEpCKXsbWg&s=10",
  "original": "https://www.backyardimage.com/wp-content/uploads/2025/01/2025-01-12-drone-Heap-0015-Pano-Edit.jpg",
  "originalWidth": 1200,
  "originalHeight": 615
}
```

The `tbs` parameter is Google's own filter string, and several filters combine with commas.

| Filter | Value |
| :--- | :--- |
| Large, medium or icon | `isz:l`, `isz:m`, `isz:i` |
| Larger than a size | `isz:lt,islt:qsvga` for 400×300, through `islt:4mp` for 2272×1704 |
| Exact size | `isz:ex,iszw:1000,iszh:1000` |
| Full colour or black and white | `ic:color`, `ic:gray` |
| A specific colour | `ic:specific,isc:red`, and the other colour names |
| Type | `itp:photo`, `itp:face`, `itp:clipart`, `itp:lineart`, `itp:animated` |

## Errors and failure paths

Plan for these rather than assuming a happy path.

**`original` is a third-party URL and it can be dead, hotlink-blocked or slow.** It points at the file on whichever site hosts it, so it carries none of Google's availability. Fetch it defensively, and fall back to `thumbnail`, which is served from Google's CDN.

**`thumbnail` is a Google CDN link, not an embedded image.** It is a real URL rather than a data URI, so displaying one still costs a request to `gstatic.com`.

**A page carries 100 results, not 10.** Cost is per call rather than per result, so one call covering 100 images is the cheapest shape here. Reach for `ijn` only past the first hundred.

**`ijn` starts at zero.** Passing `1` gets the second page, not the first.

**Dimensions describe the original, not the thumbnail.** `originalWidth` and `originalHeight` are the file behind `original`, so a size filter applied locally has to read those rather than measure what you displayed.

**`tbs` values are Google's, and an unrecognised one is ignored rather than rejected.** A filter that silently does nothing looks the same as a filter that found everything, so check that the results actually narrowed before trusting a `tbs` string you assembled by hand.

**`domain`, `gl` and `hl` are three separate axes.** The domain is which Google answers, the country code is where the search comes from, and the language is the interface. Changing one moves the results less than you expect.

Results that carry data also carry a `requestMetadata.id` worth quoting in support.

## Pricing, free tier and limits

The Google Images tool costs **5 credits per successful call**. Response size does not change the price, and a call returns 100 results, which makes this one of the cheaper tools in the catalogue per row returned.

The free tier is **1,000 credits every month with no card**, which is 200 Google Images calls at the base rate. It renews with the billing cycle, so a low-volume agent runs on the free tier indefinitely.

Paid plans start at **$59 a month** for 200,000 credits, which is 40,000 calls. The unit price falls with volume, from **$1.48 per 1,000 calls** on the entry plan to **$0.60** on Basic and **$0.41** across the Growth tiers. Current figures live on the [pricing page](https://hasdata.com/prices?utm_source=github&utm_medium=syndication&utm_campaign=google-images-mcp).

Your plan also sets concurrency. The free tier allows 1 request at a time, Startup 5, Basic 15, and the Growth tiers run from 50 to 500. Retry on the 429 with a backoff in anything unattended, because an agent that sweeps a keyword list will reach the ceiling before you do.

A request that comes back non-200 is not billed. A successful call that finds nothing is still a call.

## How it compares

Google's Custom Search JSON API is the official route to image results, and it is a narrower instrument.

| | Custom Search JSON API | This server |
| :--- | :--- | :--- |
| Eligibility | A Google Cloud project and a search engine you configure | An API key |
| Results per call | 10 | 100 |
| Daily ceiling | 10,000 queries a day, free tier of 100 | Your plan's credits |
| Scope | The sites your programmable engine covers | The public Google Images page |
| Filter set | A documented subset | Google's own `tbs` string |
| Full-resolution URL | Returned | Returned |

The row that decides it is scope. A programmable search engine covers what you configure it to cover, and searching the open web with it takes extra setup and still behaves differently from the page a person sees. When you control the sites you want to search, the official API is free at low volume and the better fit.

## FAQ

### Is there an official Google Images MCP server?

Google does not publish one. This one is maintained by HasData and reads public Google Images pages.

### What is a Google Images MCP server?

An MCP server exposes tools an AI client can call. This one turns a Google Images results page into JSON an agent can reason over, without a browser or a scraping library in your stack.

### Do I need a Google account or a Cloud project?

No. The only credential is your HasData key.

### Can I download the images?

The response gives you `original`, the URL of the file on the site that hosts it. Whether you may download, store or reuse that file is a copyright question about that image, and it is not answered by the fact that Google indexed it. Assume an image is protected unless you have checked otherwise.

### Can I search by image rather than by text?

No. This tool takes a text query. Reverse image search is a different endpoint.

### How do I filter by size?

Through `tbs`. `isz:l` gets large images, `isz:lt,islt:2mp` gets anything above 1600×1200, and `isz:ex,iszw:1000,iszh:1000` gets exactly 1000×1000. Filters combine with commas.

### Can I use this together with other HasData APIs?

Yes. One key covers everything, and one endpoint serves them all through the `apis` parameter. Point a client at `?apis=google_images,google_serp` to get both tool sets in one connection, or at [`mcp.hasdata.com/api/mcp`](https://docs.hasdata.com/mcp-server?utm_source=github&utm_medium=syndication&utm_campaign=google-images-mcp) for the full catalogue.

### Is HasData affiliated with Google?

No. HasData is an independent service and is not affiliated with, endorsed by, or sponsored by Google. Google is a trademark of its respective owner. The tools work with publicly available data only, and you are responsible for using the results in line with Google's terms and the law that applies to you.

### Compliance and personal data

Image search returns whatever the query matched, so a query naming a person returns pictures of that person, and `itp:face` asks for faces specifically. Images of identifiable people are personal data, and in several jurisdictions biometric processing is regulated separately and more strictly again. Building a face set from these results is the case to think hardest about before you start, rather than after.

## HasData links

- [Google Images API](https://hasdata.com/apis/google-images-api?utm_source=github&utm_medium=syndication&utm_campaign=google-images-mcp), the REST endpoint behind this tool
- [API documentation](https://docs.hasdata.com/apis/google-images/images?utm_source=github&utm_medium=syndication&utm_campaign=google-images-mcp)
- [MCP server documentation](https://docs.hasdata.com/mcp-server?utm_source=github&utm_medium=syndication&utm_campaign=google-images-mcp)
- [Pricing](https://hasdata.com/prices?utm_source=github&utm_medium=syndication&utm_campaign=google-images-mcp)
- [Dashboard](https://app.hasdata.com/sign-up?utm_source=github&utm_medium=syndication&utm_campaign=google-images-mcp)

Other HasData MCP servers: [Google Search](https://github.com/HasData/google-search-mcp), [Google Maps](https://github.com/HasData/google-maps-mcp), [Google Trends](https://github.com/HasData/google-trends-mcp), [Google Flights](https://github.com/HasData/google-flights-mcp), [Bing](https://github.com/HasData/bing-mcp), [DuckDuckGo](https://github.com/HasData/duckduckgo-mcp), [YouTube](https://github.com/HasData/youtube-mcp), [TikTok](https://github.com/HasData/tiktok-mcp), [Instagram](https://github.com/HasData/instagram-mcp), [Amazon](https://github.com/HasData/amazon-mcp), [Walmart](https://github.com/HasData/walmart-mcp), [Shopify](https://github.com/HasData/shopify-mcp), [Yelp](https://github.com/HasData/yelp-mcp), [Yellow Pages](https://github.com/HasData/yellowpages-mcp), [Zillow](https://github.com/HasData/zillow-mcp), [Redfin](https://github.com/HasData/redfin-mcp), [Airbnb](https://github.com/HasData/airbnb-mcp), [Booking.com](https://github.com/HasData/booking-mcp), [Indeed](https://github.com/HasData/indeed-mcp), [Glassdoor](https://github.com/HasData/glassdoor-mcp).

## Development

The launcher is a thin stdio bridge to the remote server, so there is nothing to build.

```bash
npm install
HASDATA_API_KEY=your_key_here npm test
```

The tests in `test/` assert the tool contract, the part that can break without a commit here. They check that `?apis=google_images` returns the one expected tool, that its name has not changed, that it still requires `q` and carries a description, that the filter and targeting parameters this README documents are still in the schema, and that the key in use is actually accepted. That last check calls the tool for real and costs 5 credits, which is the price of a canary that can fail for the right reason.

One test asserts that a live search returns results carrying both `original` and its dimensions. Those three fields are what makes this tool worth calling over a plain SERP, and a parser change that dropped them would leave a green tools list behind it.

The contract suite also runs weekly on a schedule, because the upstream tool list can change without anyone touching this repository.

## Contributing

A tool table, a response sample or a documented behaviour that does not match reality is worth an issue. There is a template for exactly that. Pull requests are welcome for the same, and for anything in the launcher.

## License

MIT, see [LICENSE](LICENSE).
