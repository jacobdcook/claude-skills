Use this skill when the user wants to build or operate a 24/7 AI employee using AntiGravity + Gravity Claw/OpenClaw, including memory, dashboards, tasks, reminders, invoicing, content ideation, and model-cost routing.

## Mission
Turn the user's system into a persistent AI operations stack where:
- AntiGravity builds software, dashboards, and integrations.
- Gravity Claw executes assistant behavior across daily workflows.
- Memory and automation make behavior persistent and proactive.

## Prerequisites Checklist
Confirm these before implementation:
- AntiGravity account
- API access (Gemini and/or Claude)
- n8n account
- Pinecone account
- Railway account
- Supabase project
- Optional: ClickUp account
- OpenRouter API key (for multi-model switching)

## Core Architecture To Enforce
1) Dual-system separation:
- AntiGravity = builder
- Gravity Claw/OpenClaw = assistant runtime

2) Three-tier memory:
- Core memory (SQL permanent facts)
- Conversation buffer (recent detailed context + compressed summaries)
- Semantic memory (Pinecone vectors for long-term retrieval)

3) Single source of truth:
- Sync task and dashboard data to shared backends (Supabase, ClickUp)

## Execution Workflow
Follow this order unless the user asks otherwise:
1. Stand up Mission Control auto-launch.
2. Add one tracked data feature (weight tracker) and bi-directional sync.
3. Connect action system (ClickUp) as core task backend.
4. Add assistant self-improvement behavior (`soul.md` updates).
5. Enable long-term memory ingestion (URLs/docs/transcripts).
6. Add document automation (invoice generation + reusable invoice skill).
7. Add time-based automation (reminders + daily brief).
8. Add model routing/cost mode switching.

## Prompt Pack (Use/Adapt Verbatim)
Use these templates when asked to generate implementation prompts:

1) Mission Control autostart:
"Hey there dude my mission control I want you to set it up in such such a way that when I log into my laptop every day that it's already running and runs for me that I don't have to keep prompting you to open it up."

2) Weight tracker UI:
"Hey there, dude. I'd like you to add in my productivity section a weight tracker that just basically has the date, how much I weighed, and then if I give that information to Gravity Claw, I'd like him to be able to update it, and they should be reflected together based on the date. And I'd like you to do it in my brand guidelines."

3) Weight backend sync + deploy:
"Hey dude, I want you to align the weight tracking in the dashboard with gravity claw. So basically, when I tell gravity claw what my weight is and the date, it needs to connect to the same data in the back end, and it should all align together. Once you've completed this, push it live to railway, please."

4) Add weight entry:
"I'd like you to add in a weight for me. Do me a favor. Add it. On the 15th of April, 2026, I weighed 90 kg."

5) ClickUp integration:
"I'd like to connect my gravity claw to click up and that needs to be reflected in the actual dashboard itself the command center and in addition to that one key thing whenever I ask gravity claw to do anything it needs to update it in clickup that is its core point of call for anything to do with actions"

6) Add task naturally:
"Hey there, dude. Could you just add an action to my to-do list, please? We need to get Laney to go and buy 16 eggs for the Supreme Egg Festival happening, but we need to get that done by 10:00 p.m. tomorrow. Thanks so much."

7) Assign by person name:
"Hey dude, just make sure that task is assigned to Laney. And if I give a instruction to describe somebody by name, I expect you to link that up for me in the to-do list, please."

8) Self-evolving soul.md:
"Hey dude, I would like you to give Gravity Claw the ability to update its own soul.md. So if I give it instructions like when I do X, you do Y, it should have the ability to improve that and amend that itself. So I don't even have to do that within anti-gravity."

9) Long-term memory ingestion:
"Hey dude, I want you to cry for me a memory ad feature. So if I'm talking to Gravity Claw and I give it a YouTube URL or I give it a document or I do that in my mission control dashboard, I want you to be able to actually vectorize and save that information. So give it the capabilities to add this to its long-term memory."

10) Save transcript + confirm:
"Hey, dude. Could you save this transcript to your long-term memory just that you and I can chat about it later in the future and you have it as a point of reference. Also, write for me the first two sentences of this video."

11) Retrieve summary from memory:
"Hey there, dude. Give me a TLDDR of the video."

12) Store brand assets:
"Hey there, dude. I'm going to give you my brand guidelines. I'm going to give you some assets and media. And I want you to save this in the project file. This is Jack's media assets. And whenever you designing any dashboard, any UI, any documents, you are to consult these assets before you build it."

13) Branded invoice generation:
"Hey there, dude. I'd like you to create me an invoice in my invoice style. Let's make it out to the UFC. And the invoice amount is going to be $22,795. The fee is for my UFC debut. And add a $10,000 win bonus on there as well, please. Um, and then what I'd like you to do is just Yeah, show it in my chat. Go ahead and find their address, obviously, so you know who to send it to."

14) Add invoice as reusable skill:
"Awesome. Now we've actually got the perfect invoice. What I want you to do is add a new skill to gravity claw. And effectively what you're going to do is whenever I ask you to create a new invoice, you're going to create it in that style and substitute out the information. You're going to do the research on the company and add that to the document and make sure that Graphic Club does that whenever requested. In addition to that, you're also now going to go to the command center and save all the documents in the command center for me to download when I want to save both those documents in Superbase for me."

15) Reminder capability:
"Hey dude, I want to add a feature to give my gravity claw the ability to send me reminders. So it needs to have a taskuling function. For instance, if I said, Dude, in 26 minutes, send me a message. In 26 minutes, I get a message. It needs to have a taskuling function. Develop this for me, please."

16) One-off reminder:
"Hey there, dude. Could you set up a reminder to message me in one minute with the current temperature in Dubai? Thank you."

17) Daily morning brief:
"Hey, I want you to create for me a daily morning brief. This morning brief must have the following information. Any trending or breaking news that's happened in the day prior, the weather forecast for the day, any important meetings that I have coming up for the rest of the day, and anything else that you think would be important for me to know to remind me of based on our conversations um over the past previous week, so you can have a little bit of extra zazz like that. I'd like to add those additional features to my existing morning brief and send me a sample one now, please."

18) Research + ideation:
"Hey, could you do me a favor and find out, have there been any new releases on anti-gravity? And if so, could you maybe suggest one or two things that might be beneficial for me to talk about for my audience based on all the information that you know about my channel? I would like to know the um suggested YouTube title based on my previous performance and also two lines on what that video might be about."

19) Pull latest thumbnail:
"Hey dude, could you grab me the thumbnail for my latest YouTube video? Because once you connected to these things, guys, if I'm out and about, I'm like, Dude, what was my thumbnail? I'm trying to ideate."

20) Enable model switching:
"Hey dude, I would like you to give Gravity Claw the ability to change the model that it's using when requested. So if I want to go for a less intelligent model, more powerful one, do that. And the three I'd like you to suggest are Claude Sonnet for big power. And then what you can do if you want to is check the resource I created for you here, which I did just based on research essentially. And you could say Gemini Flash Deep Seek B3 like this."

21) Cheap model for simple task:
"Hey, switch to an inferior model, get the transcript, and then switch back so you can save on your costs."

## Cost Profiles (Reference)
- Low use (~50 msgs/day): Sonnet ~$12/week, Flash ~$2/week, DeepSeek v3 ~$0.50/week
- Medium use (~200 msgs/day): Sonnet ~$34/week, Flash ~$5/week, DeepSeek v3 ~$1.50/week
- Heavy use (~500 msgs/day): Sonnet ~$85/week, Flash ~$13/week, DeepSeek v3 ~$3.50/week

## Operating Rules
- Start with 1-2 features, validate end-to-end, then expand.
- Prefer natural-language prompting over rigid command syntax.
- Keep all automations observable in Mission Control.
- For every new feature, enforce: data write, dashboard reflection, and retrieval test.
- When adding recurring automation, require a sample run immediately.

## Output Contract
When this skill is used, return:
1) Current stack readiness checklist (ready/missing)
2) Recommended next feature in sequence
3) Exact prompt to paste (single best option)
4) Verification steps (what to test right now)
