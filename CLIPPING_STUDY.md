# Clipping for Money — Study, Verdict, and 30-Day Plan

_Researched November 2026. Rate figures come mostly from clipping-platform blogs and third-party campaign surveys, not official platform data, so check each live campaign page before relying on them._

---

## 1. How clipping makes money

There are three ways to get paid. Only two of them work in a 30-day window.

| Model | How you get paid | Realistic in 30 days? |
|---|---|---|
| **A. Campaign clipping** (Whop Content Rewards and similar boards) | A brand, streamer, podcaster or music label posts a budget and pays per 1,000 *approved* views on clips you post from your own accounts | **Yes.** You can start on day 1, but the money depends on views |
| **B. Clipping as a service** (retainer) | A creator pays you a flat monthly fee to clip their long videos | **Yes.** It is the most reliable, but you have to sell it |
| **C. Platform payouts** (YouTube Shorts revenue share, TikTok Creator Rewards) | The platform pays your clip channel directly | **No.** YouTube needs 1k subs + 10M Shorts views in 90 days; TikTok Creator Rewards needs original 1-minute-plus videos and mostly excludes clip content |

### What campaigns actually pay
- Advertised rates run from **$0.20 to $6 per 1,000 views**. The median is about **$1.00–$1.30**. A survey of 719 live campaigns (Oct 2026) put the median at $1.00/1k, with the middle half paying $1–$2.
- **What you actually earn is lower than the advertised rate.** One analysis estimates a blended **≈ $0.39 per 1,000 views** once you count:
  - **Per-clip minimums.** Views below the threshold earn $0, and most clips on new accounts never reach it.
  - **Per-clip and per-clipper caps.** A viral clip stops paying at the cap.
  - **Budget exhaustion.** Views after the budget runs out earn nothing, and popular campaigns drain within days.
  - **Rejections.** A clip that breaks *any* rule in the brief earns $0, even at 200k views.
  - **Review windows.** Views are often held for review for about 72 hours before they count.
- Platforms and agencies take their cut from the brand's side (roughly a 7–10% platform fee, up to 30% for agency-managed campaigns). That cut lowers the rate a brand can offer.

### Platform risk
- **YouTube** "reused content" and "inauthentic / mass-produced content" rules apply to clips. Low-effort clip channels get demonetized and their reach is throttled. An October 2026 algorithm update reportedly gives original Shorts more distribution and cuts it for unoriginal ones.
- **Meta and TikTok** both suppress unmodified reposts. If you post the same rendered file across many accounts, the duplicates get flagged.
- **What this means for the pipeline:** every clip needs visible transformation (reframing, captions, hook text, cuts). Never post the identical file to several accounts.

---

## 2. Critique of the prompt the other AI wrote

**Summary:** it is a reasonable *video-engineering* spec, but it is not a *money* plan. It builds a renderer and stops there. It never touches the parts that decide whether you get paid: the campaign brief, distribution, approval rules, tracking, and getting clients.

### Problems that will break it or cost you money (worst first)
1. **The business layer is missing.** Campaign briefs set the clip length, required tags, banned topics, required source and caption rules. The prompt hard-codes its own rules (18–45s, its own metadata) and asks for the brief *after* building everything. The brief has to be the input that drives every step, not an afterthought.
2. **"Virality score, reject below 75" is a made-up number.** Nothing defines the score. faster-whisper only transcribes, so some LLM has to score the clips, and the prompt never says which one or how. An uncalibrated 0–100 self-score from an LLM is noise. A cutoff of 75 will pass junk or reject good clips at random. You need a rubric with sub-scores, a top-N ranking, and then calibration against real view data.
3. **"Download high-resolution videos" has no rights check.** Clipping works legally because the campaign or client *authorizes* the source. Downloading random YouTube videos to clip and monetize is copyright infringement, and it is also how accounts get struck. The pipeline has to take the source from the brief or the client.
4. **Face detection is not active-speaker detection.** MediaPipe *face detection* finds faces. It cannot tell who is talking. On a two-person podcast, the most common clip source, the crop will jump between faces or sit on the listener. You need mouth-movement scoring (Face Mesh lip distance against audio energy), a "two faces → stacked split-screen" fallback, and a blurred-background fallback when no face is found.
5. **Trimming dead air breaks subtitle sync** unless word timestamps are re-timed after every cut. The prompt doesn't mention this, and it is the most common bug in this kind of pipeline.
6. **"68–75px font" means nothing without the ASS canvas size.** ASS font size is relative to `PlayResY`. You have to set `PlayResX=1080, PlayResY=1920`, or the sizes won't come out as specified.
7. **There is no QA step.** Nothing checks that the output isn't cropping the wrong person, that captions don't cover the face, or that the audio is in sync. Claude can read images, so the pipeline should render a contact sheet of frames and inspect it before shipping.
8. **There is no distribution, tracking or feedback loop.** Where do clips go, from which accounts, when? Which campaign, which clip, how many views, approved or rejected, and how much was paid? Without a tracker you can't tell what's working, and you can't recalibrate the scoring.
9. **The environment would fail.** This container runs Python 3.13. MediaPipe wheels have lagged behind new Python versions, so pin a **3.11 or 3.12 venv**. yt-dlp is not installed here. On CPU, faster-whisper needs `int8` and a sensible model size (`small` or `medium`), or it will be painfully slow.

### Questionable design choices
- **A fixed 18–45s length.** Many campaigns want 15–60s, and some reward longer clips. Read the length from the brief.
- **"Platform-specific captions under 150 characters with hashtags"** is fine. But the "campaign compliance checklist" has to be *generated from the actual brief*, not a generic template.
- **No originality or variation strategy.** That is a real risk given the YouTube and Meta enforcement described in section 1.

### What it gets right
- Word-level timestamps → ASS karaoke subtitles is the correct technical approach. It is lighter and more controllable than burning subtitles with moviepy.
- Keeping the workflow modular is good. Keep it.

---

## 3. Verdict: the most realistic way to make money in the next 30 days

**The pipeline is a productivity tool, not the business.** It lets one person produce 20–40 good clips a day instead of 5. The money comes from two sources, and you should run both at once:

1. **Campaign clipping**, for fast but variable cash. You can start in days 2–3. The money is lumpy and depends on views.
2. **Retainer clients**, for reliable cash. Sell the pipeline's output to podcasters and streamers as a monthly service. One client at $300–$800/month is worth more than most beginners' entire first month of campaign earnings.

### Honest numbers
- To make **$1,000 from campaigns at a $1 CPM you need 1,000,000 approved views**. At the realistic blended rate (~$0.40), you need about 2.5M.
- A realistic month one for a new clipper working hard (3 platforms, 3–5 clips per day per platform, about 300–400 clips) is **roughly $0–$500 from campaigns**. A single clip that takes off can push that past $1,000, but you can't plan on it.
- **Retainers:** 20–30 personalized outreach messages a day with free sample clips typically convert 1–3 clients in weeks 2–4, at **$300–$1,500/month each**.
- **Combined realistic target for 30 days: $300–$2,000.** Anyone promising $5k–10k in month one is selling a course.

### The 30-day plan
| Days | Do this |
|---|---|
| 1–2 | Build the pipeline using the prompt in `MASTER_PROMPT.md`. Create **fresh accounts** on TikTok, Instagram Reels and YouTube Shorts, one per niche, and warm them up by using them like a normal viewer for 2–3 days before posting. |
| 2–3 | Join **3–5 campaigns**. Prefer ones with ≥ $1 CPM, **at least 50% of the budget left**, a low per-clip minimum, a high cap, and long-form source material (podcasts and streams give you many clips per video). Skip music campaigns at first, because they are crowded. |
| 3–10 | Post **3–5 clips/day per platform**. Log every clip in the tracker. Submit each clip immediately after posting. Read each brief twice, because rejections are the biggest leak. |
| 7 onward | Start outreach: **20/day** to podcasters and streamers with 10k–200k subscribers who don't already post clips. The offer: "Here are 3 clips I already made from your last episode, free. Want 30 a month?" |
| 10–20 | Use the data. Cut campaigns and hook styles that are underperforming, and double the volume on the top 20% of clip *types*, not just the top campaigns. Recalibrate the scoring rubric against real view counts. |
| 20–30 | Close 1–3 retainers. Keep the campaign volume going, now with proven hook patterns. |

### Don't do these (they get you banned or unpaid)
- Bought views, engagement pods, or bot-based auto-posting. They get rejected, and they get accounts banned.
- Clipping content you have no rights or brief for.
- Posting the identical file to multiple accounts.
- Paying for "clipping masterclasses." Everything they teach is in this document.

---

## Sources
- [Clipping Campaign Data 2026: Rates, Costs and Views From 719 Campaigns](https://clippingcollective.com/guides/clipping-campaign-data-2026)
- [Whop Content Rewards Earnings: Real 2026 Numbers](https://autoclip.dev/blog/how-much-do-whop-content-rewards-clippers-make-2026)
- [Whop Clipping: $0.20 to $6 per 1000 Views (2026)](https://openclip.app/guides/whop-clipping-guide)
- [Whop Clipping: How to Make Money with Content Rewards (2026)](https://reap.video/blog/whop-clipping)
- [What Is CPM in Clipping? 2026 Rate Benchmarks](https://forkoff.xyz/blog/clipping/cpm-rates-for-clipping)
- [Clipping on TikTok: Payouts, Rules, Specs](https://openclip.app/guides/clipping-on-tiktok)
- [GTA 6 Clipping campaign example](https://contentrewards.com/articles/gta-6-clipping)
- [Clipping Campaigns: How They Work (2026)](https://viral.app/blog/guides/clipping-campaigns)
- [YouTube channel monetization policies](https://support.google.com/youtube/answer/1311392)
- [Tubefilter: YouTube inauthentic content policy update (July 2026)](https://www.tubefilter.com/2026/07/13/youtube-inauthentic-content-monetization-policy-update/)
- [vidIQ: YouTube Reused Content Policy 2026](https://vidiq.com/blog/post/youtube-reused-content-policy-guide/)
- [Search Engine Journal: Meta follows YouTube in crackdown on unoriginal content](https://www.searchenginejournal.com/meta-follows-youtube-in-crackdown-on-unoriginal-content/551096/)
