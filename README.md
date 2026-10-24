# InterviewPilot: live AI HR interviewer on ASI:One

![tag:innovationlab](https://img.shields.io/badge/innovationlab-3D8BD3)
![tag:hackathon](https://img.shields.io/badge/hackathon-5F43F1)

Paste a job description into ASI:One. **Priya**, an AI HR recruiter, interviews you for that exact role in a natural, continuous conversation. She follows up on vague answers, probes your claims, and moves through every JD requirement until you say **stop**. You then get a hiring scorecard that rates you against each requirement and includes a 7-day prep plan.

Two ways to take the interview:
- **💬 In ASI:One chat**: the entire workflow happens inside the conversation.
- **🎥 Face-to-face room**: the chat gives you a link to a live voice and video interview. An animated HR avatar speaks each question, stops talking the moment you start (barge-in), and replies when you pause. The scorecard comes back into ASI:One when you type `result`.

## Problem, users, outcome
- **Problem:** candidates rarely get realistic interview practice tailored to the job they're applying for. Human mock interviews are expensive and hard to schedule.
- **Target users:** students, freshers and job switchers preparing for interviews.
- **Outcome:** a role-specific live interview, then an actionable scorecard: overall score, verdict, a 0–10 score per JD requirement with evidence, strengths, gaps, and a 7-day prep plan.

## Agent
| Name | Address | Protocol |
|---|---|---|
| InterviewPilot | `agent1qwwgjk95f769glj6ntawp0ytdkrypx8ww480a68hy7tk0fqpdlva7xuuvvm` | Agent Chat Protocol (ACP) |

Agentverse profile: _add link_ · ASI:One shared chat: _add link_ · Demo video: _add link_

## How it works
```
ASI:One user ──ACP──> InterviewPilot (uAgent, Agentverse mailbox)
                           │
                           └──HTTPS──> InterviewPilot service (hosted)
                                         ├─ ASI1 LLM: interviewer turns + scorecard
                                         └─ /room/<id>: face-to-face interview page
```
- **uAgents + Agent Chat Protocol:** discoverable in ASI:One and usable entirely inside the chat.
- **ASI1 (`asi1-mini`)** drives every interviewer turn, using the full JD and conversation history, and writes the final scorecard.
- The interview engine runs as a hosted service operated by the team. This repo contains the agent, which connects to that service.
- Chat and video share one interview session, so you can start in chat and continue face to face.

## Try it in ASI:One
1. Search for **InterviewPilot** in ASI:One, or open the Agentverse profile and click "Chat".
2. Paste a job description (a few lines or more).
3. Answer Priya's questions in chat, or open the 🎥 link.
4. Type **stop** to end the interview and get your scorecard. After a video interview, type **result**.

## Run the agent locally
```bash
python3 -m venv .venv && source .venv/bin/activate
pip install uagents uagents-core httpx python-dotenv
cp .env.example .env    # fill in the values
python agent.py         # open the printed inspector link → Connect → Mailbox
```
On macOS with python.org Python, run `export SSL_CERT_FILE=$(python -c "import certifi;print(certifi.where())")` first.

## Environment
| Variable | Purpose |
|---|---|
| `AGENT_SEED` | Secret seed that fixes the agent's address |
| `BACKEND_URL` | Base URL of the InterviewPilot service |
| `BACKEND_TOKEN` | Access token for the service |
| `PUBLIC_URL` | Public base URL for face-to-face room links |

## Reliability
- Every message is acknowledged. If a call to the service fails, the user gets a friendly retry message, never silence.
- ASI1 calls retry once when the connection drops.
- The room page falls back to typed answers if the browser has no microphone or speech support, and works without a camera.
