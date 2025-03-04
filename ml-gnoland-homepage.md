package home

import (
	"std"
	"strconv"

	"gno.land/p/demo/ownable"
	"gno.land/p/demo/ui"
	"gno.land/p/moul/dynreplacer"
	blog "gno.land/r/gnoland/blog"
	events "gno.land/r/gnoland/events"
	"gno.land/r/leon/hof"
)

var (
	override string
	Admin    = ownable.NewWithAddress("g1manfred47kzduec920z88wfr64ylksmdcedlf5") // @moul
)

func Render(_ string) string {
	r := dynreplacer.New()
	r.RegisterCallback(":latest-blogposts:", func() string {
		return blog.RenderLastPostsWidget(4)
	})
	r.RegisterCallback(":upcoming-events:", func() string {
		out, _ := events.RenderEventWidget(events.MaxWidgetSize)
		return out
	})
	r.RegisterCallback(":latest-hof:", func() string {
		return hof.RenderExhibWidget(5)
	})
	r.RegisterCallback(":qotb:", quoteOfTheBlock)
	r.RegisterCallback(":chain-height:", func() string {
		return strconv.Itoa(int(std.ChainHeight()))
	})

	template := `# Welcome to gno.land

## We’re building a user-owned internet where governance is fair, development is open, and innovation is rewarded.

[Learn more](/about) | [GitHub](https://github.com/gnolang) | [Try Gno](https://play.gno.land)

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Vivamus ultricies ultricies risus. Cras libero est, congue vitae ex nec, sodales rutrum leo. Quisque a blandit ex, at viverra ex.Donec nulla risus, ullamcorper rutrum sapien ut, varius interdum quam.

---

## gno.land’s beta mainnet is live

Lorem ipsum dolor sit amet.

**Enter your public Cosmos or Gno address:**

[ Check Button ]

Read more about the [airdrop criteria here](#)

---

## A new decentralized operating system (dOS) to power the Internet of Everyone

- **Social coordination & community impact**
- Package examples and realm examples
- **Governance & Organization**
- package examples and realm examples
- **New Economic Model**
- GovDAO
- **The next killer app**

---

## Discover the gno.land universe

- [Wallets](/wallets)
- [Explorers](/explorers)
- [DeFi](/defi)
- [Social dApps](/social-dapps)

---

## A community of gnomes

### Our Blog
:latest-blogposts:

### Past Events
:upcoming-events:

---

## Build on gno.land

- [Builder Portal](/builder-portal)
- [Bounties](/bounties)
- [gno.land grants](/grants)

---

## Stay up to date with the latest updates

Sign up for our newsletter.

[ Input Field ] [ Subscribe Button ]

I understand my information will be processed in accordance with the [Privacy Policy](#), and I agree to the Terms of Service.

---

## Quote of the Block#:chain-height:

> :qotb:

---

**This is a testnet.**
Package names are not guaranteed to be available for production.`

	if override != "" {
		template = override
	}
	result := r.Replace(template)
	return result
}

func quoteOfTheBlock() string {
	quotes := []string{
		"Gno is for Truth.",
		"Gno is for Social Coordination.",
		"Gno is _not only_ for DeFi.",
		"Now, you Gno.",
		"Come for the Go, Stay for the Gno.",
	}
	height := std.ChainHeight()
	idx := int(height) % len(quotes)
	qotb := quotes[idx]
	return qotb
}

func AdminSetOverride(content string) {
	Admin.AssertCallerIsOwner()
	override = content
}
