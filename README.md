# peasy-compress-go

[![Go Reference](https://pkg.go.dev/badge/github.com/peasytools/peasy-compress-go.svg)](https://pkg.go.dev/github.com/peasytools/peasy-compress-go)
[![Go Report Card](https://goreportcard.com/badge/github.com/peasytools/peasy-compress-go)](https://goreportcard.com/report/github.com/peasytools/peasy-compress-go)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)

Go client for the [Peasy Tools](https://peasytools.com) compression API -- compress, archive, and extract files with ZIP, TAR, gzip, Brotli, and Zstandard. Zero dependencies beyond the Go standard library.

Built from [Peasy Tools](https://peasytools.com), a comprehensive file compression toolkit offering free online tools for creating and extracting archives across multiple formats. The glossary covers compression algorithms from classic DEFLATE to modern Brotli and Zstandard, while guides compare archive formats and explain lossless vs lossy compression strategies.

> **Explore compression formats at [peasytools.com](https://peasytools.com)** -- [ZIP](https://peasytools.com/formats/zip/), [TAR](https://peasytools.com/formats/tar/), [Gzip](https://peasytools.com/formats/gz/), and more.

<p align="center">
  <img src="demo.gif" alt="peasy-compress-go demo -- ZIP, TAR, and gzip compression tools in Go terminal" width="800">
</p>

## Table of Contents

- [Install](#install)
- [Quick Start](#quick-start)
- [What You Can Do](#what-you-can-do)
  - [Compression and Archive Tools](#compression-and-archive-tools)
  - [Browse Compression Reference Content](#browse-compression-reference-content)
  - [Search and Discovery](#search-and-discovery)
- [API Client](#api-client)
  - [Available Methods](#available-methods)
- [Learn More About Compression](#learn-more-about-compression)
- [Also Available](#also-available)
- [Peasy Developer Tools](#peasy-developer-tools)
- [License](#license)

## Install

```bash
go get github.com/peasytools/peasy-compress-go
```

Requires Go 1.21+.

## Quick Start

```go
package main

import (
	"context"
	"fmt"
	"log"

	peasy "github.com/peasytools/peasy-compress-go"
)

func main() {
	client := peasy.New()
	ctx := context.Background()

	// List available compression tools
	tools, err := client.ListTools(ctx, nil)
	if err != nil {
		log.Fatal(err)
	}
	for _, t := range tools.Results {
		fmt.Printf("%s: %s\n", t.Name, t.Description)
	}
}
```

## What You Can Do

### Compression and Archive Tools

File compression reduces storage costs and speeds up network transfers. Archive formats like ZIP and TAR bundle multiple files into a single container, while compression algorithms like DEFLATE, Brotli, and Zstandard reduce the size of the archived data. Understanding which format to use -- ZIP for cross-platform compatibility, TAR+gzip for Unix workflows, or Brotli for web content delivery -- is critical for choosing the right tool for each task.

| Format | Type | Best For |
|--------|------|----------|
| ZIP | Archive + Compression | Cross-platform file sharing, per-file compression |
| TAR | Archive only | Unix/Linux bundles, combined with gzip or Brotli |
| gzip | Compression only | Single file compression, HTTP content encoding |
| Brotli | Compression only | Web assets, higher compression ratios than gzip |
| Zstandard | Compression only | Fast real-time compression, database backups |

```go
package main

import (
	"context"
	"fmt"
	"log"

	peasy "github.com/peasytools/peasy-compress-go"
)

func main() {
	client := peasy.New()
	ctx := context.Background()

	// Fetch the ZIP compression tool details
	tool, err := client.GetTool(ctx, "zip-compress")
	if err != nil {
		log.Fatal(err)
	}
	fmt.Printf("Tool: %s\n", tool.Name)           // ZIP compression tool
	fmt.Printf("Category: %s\n", tool.Category)   // Archive and compression category

	// List supported compression and archive formats
	formats, err := client.ListFormats(ctx)
	if err != nil {
		log.Fatal(err)
	}
	for _, f := range formats.Results {
		fmt.Printf("%s (%s): %s\n", f.Name, f.Extension, f.MimeType)
	}
}
```

Learn more: [ZIP Format Reference](https://peasytools.com/formats/zip/) · [TAR Format Reference](https://peasytools.com/formats/tar/) · [Archive Formats Compared](https://peasytools.com/guides/archive-formats-compared/)

### Browse Compression Reference Content

The compression glossary explains key concepts from basic archiving terminology to advanced algorithm details. Understanding the difference between lossless and lossy compression, how DEFLATE works under the hood, and when to choose Brotli over gzip helps developers make informed decisions about their compression pipelines.

| Glossary Term | Description |
|---------------|-------------|
| Archive | A single file containing multiple files and directory structures |
| TAR | Tape Archive format that bundles files without compression |
| Brotli | Google-developed compression algorithm optimized for web content |
| Lossless Compression | Compression that preserves all original data upon decompression |
| Zstandard | Facebook-developed algorithm balancing speed and compression ratio |

```go
// Browse compression glossary terms programmatically
glossary, err := client.ListGlossary(ctx, &peasy.ListOptions{Search: str("deflate")})
if err != nil {
	log.Fatal(err)
}
for _, term := range glossary.Results {
	fmt.Printf("%s: %s\n", term.Term, term.Definition) // Compression algorithm definition
}

// Read in-depth guides comparing archive formats
guides, err := client.ListGuides(ctx, &peasy.ListGuidesOptions{Category: str("archiving")})
if err != nil {
	log.Fatal(err)
}
for _, g := range guides.Results {
	fmt.Printf("%s (%s)\n", g.Title, g.AudienceLevel) // Guide title and difficulty level
}
```

Learn more: [Archive Glossary](https://peasytools.com/glossary/archive/) · [Brotli Glossary](https://peasytools.com/glossary/brotli/) · [Lossless vs Lossy Compression Guide](https://peasytools.com/guides/lossless-vs-lossy-compression-guide/)

### Search and Discovery

The unified search endpoint queries across all compression tools, glossary terms, guides, and supported file formats simultaneously. This is useful for building CLI tools, documentation search, or CI/CD integrations that need to find the right compression approach.

```go
// Search across all compression tools, glossary, and guides
results, err := client.Search(ctx, "gzip archive", nil)
if err != nil {
	log.Fatal(err)
}
fmt.Printf("Found %d tools, %d glossary terms\n",
	len(results.Results.Tools),
	len(results.Results.Glossary)) // Cross-content compression search results
```

Learn more: [TAR Glossary](https://peasytools.com/glossary/tar/) · [Lossless Compression Glossary](https://peasytools.com/glossary/lossless-compression/) · [Zstandard Glossary](https://peasytools.com/glossary/zstandard/)

## API Client

The client wraps the [Peasy Tools REST API](https://peasytools.com/developers/) with typed Go structs and zero external dependencies.

```go
client := peasy.New()
// Or with a custom base URL:
// client := peasy.New(peasy.WithBaseURL("https://custom.example.com"))
ctx := context.Background()

// List tools with pagination
tools, _ := client.ListTools(ctx, &peasy.ListOptions{Page: 1, Limit: 10})

// Get a specific tool by slug
tool, _ := client.GetTool(ctx, "zip-compress")
fmt.Println(tool.Name, tool.Description)

// Search across all content
results, _ := client.Search(ctx, "gzip archive", nil)
fmt.Printf("Found %d tools\n", len(results.Results.Tools))

// Browse the glossary
glossary, _ := client.ListGlossary(ctx, &peasy.ListOptions{Search: str("deflate")})
for _, term := range glossary.Results {
	fmt.Printf("%s: %s\n", term.Term, term.Definition)
}

// Discover guides
guides, _ := client.ListGuides(ctx, &peasy.ListGuidesOptions{Category: str("archiving")})
for _, g := range guides.Results {
	fmt.Printf("%s (%s)\n", g.Title, g.AudienceLevel)
}

// List file format conversions
conversions, _ := client.ListConversions(ctx, &peasy.ListConversionsOptions{Source: str("zip")})

// Get format details
format, _ := client.GetFormat(ctx, "gzip")
fmt.Printf("%s (%s): %s\n", format.Name, format.Extension, format.MimeType)
```

Helper for optional string parameters:

```go
func str(s string) *string { return &s }
```

### Available Methods

| Method | Description |
|--------|-------------|
| `ListTools(ctx, opts)` | List tools (paginated, filterable) |
| `GetTool(ctx, slug)` | Get tool by slug |
| `ListCategories(ctx, opts)` | List tool categories |
| `ListFormats(ctx, opts)` | List file formats |
| `GetFormat(ctx, slug)` | Get format by slug |
| `ListConversions(ctx, opts)` | List format conversions |
| `ListGlossary(ctx, opts)` | List glossary terms |
| `GetGlossaryTerm(ctx, slug)` | Get glossary term |
| `ListGuides(ctx, opts)` | List guides |
| `GetGuide(ctx, slug)` | Get guide by slug |
| `ListUseCases(ctx, opts)` | List use cases |
| `Search(ctx, query, limit)` | Search across all content |
| `ListSites(ctx)` | List Peasy sites |
| `OpenAPISpec(ctx)` | Get OpenAPI specification |

Full API documentation at [peasytools.com/developers/](https://peasytools.com/developers/).
OpenAPI 3.1.0 spec: [peasytools.com/api/openapi.json](https://peasytools.com/api/openapi.json).

## Learn More About Compression

- **Formats**: [ZIP](https://peasytools.com/formats/zip/) · [TAR](https://peasytools.com/formats/tar/) · [Gzip](https://peasytools.com/formats/gz/) · [All Formats](https://peasytools.com/formats/)
- **Guides**: [Archive Formats Compared](https://peasytools.com/guides/archive-formats-compared/) · [Lossless vs Lossy Compression](https://peasytools.com/guides/lossless-vs-lossy-compression-guide/) · [All Guides](https://peasytools.com/guides/)
- **Glossary**: [Archive](https://peasytools.com/glossary/archive/) · [TAR](https://peasytools.com/glossary/tar/) · [Brotli](https://peasytools.com/glossary/brotli/) · [Lossless Compression](https://peasytools.com/glossary/lossless-compression/) · [Zstandard](https://peasytools.com/glossary/zstandard/) · [All Terms](https://peasytools.com/glossary/)
- **API**: [REST API Docs](https://peasytools.com/developers/) · [OpenAPI Spec](https://peasytools.com/api/openapi.json)

## Also Available

| Language | Package | Install |
|----------|---------|---------|
| **Python** | [peasy-compress](https://pypi.org/project/peasy-compress/) | `pip install "peasy-compress[all]"` |
| **TypeScript** | [peasy-compress](https://www.npmjs.com/package/peasy-compress) | `npm install peasy-compress` |
| **Rust** | [peasy-compress](https://crates.io/crates/peasy-compress) | `cargo add peasy-compress` |
| **Ruby** | [peasy-compress](https://rubygems.org/gems/peasy-compress) | `gem install peasy-compress` |

## Peasy Developer Tools

Part of the [Peasy Tools](https://peasytools.com) open-source developer ecosystem.

| Package | PyPI | npm | Go | Description |
|---------|------|-----|----|-------------|
| peasy-pdf | [PyPI](https://pypi.org/project/peasy-pdf/) | [npm](https://www.npmjs.com/package/peasy-pdf) | [Go](https://pkg.go.dev/github.com/peasytools/peasy-pdf-go) | PDF merge, split, rotate, compress -- [peasypdf.com](https://peasypdf.com) |
| peasy-image | [PyPI](https://pypi.org/project/peasy-image/) | [npm](https://www.npmjs.com/package/peasy-image) | [Go](https://pkg.go.dev/github.com/peasytools/peasy-image-go) | Image resize, crop, convert, compress -- [peasyimage.com](https://peasyimage.com) |
| peasy-audio | [PyPI](https://pypi.org/project/peasy-audio/) | [npm](https://www.npmjs.com/package/peasy-audio) | [Go](https://pkg.go.dev/github.com/peasytools/peasy-audio-go) | Audio trim, merge, convert, normalize -- [peasyaudio.com](https://peasyaudio.com) |
| peasy-video | [PyPI](https://pypi.org/project/peasy-video/) | [npm](https://www.npmjs.com/package/peasy-video) | [Go](https://pkg.go.dev/github.com/peasytools/peasy-video-go) | Video trim, resize, thumbnails, GIF -- [peasyvideo.com](https://peasyvideo.com) |
| peasy-css | [PyPI](https://pypi.org/project/peasy-css/) | [npm](https://www.npmjs.com/package/peasy-css) | [Go](https://pkg.go.dev/github.com/peasytools/peasy-css-go) | CSS minify, format, analyze -- [peasycss.com](https://peasycss.com) |
| **peasy-compress** | [PyPI](https://pypi.org/project/peasy-compress/) | [npm](https://www.npmjs.com/package/peasy-compress) | [Go](https://pkg.go.dev/github.com/peasytools/peasy-compress-go) | **ZIP, TAR, gzip compression -- [peasytools.com](https://peasytools.com)** |
| peasy-document | [PyPI](https://pypi.org/project/peasy-document/) | [npm](https://www.npmjs.com/package/peasy-document) | [Go](https://pkg.go.dev/github.com/peasytools/peasy-document-go) | Markdown, HTML, CSV, JSON conversion -- [peasyformats.com](https://peasyformats.com) |
| peasytext | [PyPI](https://pypi.org/project/peasytext/) | [npm](https://www.npmjs.com/package/peasytext) | [Go](https://pkg.go.dev/github.com/peasytools/peasytext-go) | Text case conversion, slugify, word count -- [peasytext.com](https://peasytext.com) |

## License

MIT
