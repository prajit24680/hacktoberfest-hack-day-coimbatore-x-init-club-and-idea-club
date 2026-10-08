# [CampusWatch]

> [Smart Campus Management System for Predictive Resource Optimization and Infrastructure Planning]

## Team

**Team Name:** [Data Alchemists]


| Member | Contribution   |
| ------ | -------------- |
| [Prajit Anand] | [Prototype Build] |
| [Sriram S] | [Prototype Build] |
| [Mohit Vaswani] | [Research and Development] |
| [Tanishq Borah] | [Research and Development] |


## Problem Statement

### The Problem

[Every day, universities lose large amounts of electricity to things nobody notices: an AC or fan left running in an empty room, lights on overnight, or a sudden power spike that signals a faulty appliance. Campuses can't see this waste because power use isn't tied to who is actually in each room, so nobody knows which room to check or who to tell. CampusWatch fixes this with an interactive 3D campus where you drill from building to block, floor and room, compare each room's live consumption with what is normal for its occupants, and alert the responsible person the moment power is wasted or abnormal. It runs on an open-weight language model that turns raw anomalies into clear, friendly alerts and explanations, so the whole system is open source, works offline, and can be adopted by any campus to cut energy bills and carbon emissions.]

### Why We Chose This Problem

[We chose this problem because we live it every day: ACs, fans and lights are left on in empty rooms across campus, and nobody knows which room or whose responsibility it is. Electricity is tracked per building, not per room, so the waste stays invisible even though it adds up to real cost and carbon. It also suits an open-weight model, which turns detected anomalies into clear alerts while running offline and keeping room-level data private. And because the layout and data are configurable, any campus can adopt it, which makes it a measurable, reusable solution rather than a one-off demo.]

## Solution

[Solution: CampusWatch is an interactive 3D energy monitor for a university campus. It links each room's power consumption to whether people are actually in it, compares it with what is normal for that room, and alerts the responsible person when power is wasted or abnormal. It has three parts: a 3D interface for exploring the campus, a rule-based detector that decides when something is wrong, and an open-weight language model that writes clear alerts and explains them.

How the prototype addresses the problem: In the prototype, you hover over a Building, pick a block, then a floor, then a room (for example C305), and see its live power against the expected level. The detector runs on a simulated clock, so when a room empties with the AC or fan still running, or consumption suddenly spikes, it raises an alert within minutes. The alert reaches the room's occupant and escalates to the warden if ignored, and the language model words it in plain language, such as which device was left on, for how long, and how much electricity was wasted. Each floor, block and building shows its wasted kWh and open alerts, so the problem is visible and measurable. The prototype runs on synthetic consumption data, since we don't have real meter feeds, but the structure is configurable, so real data could be plugged in later.]

### Key Features

- [An interactive 3D campus lets you drill from Building, floors and rooms like C305, each colored by live power status.]
- [A detector compares every room's consumption with its occupancy to catch devices left on in empty rooms and sudden abnormal spikes.]
- [Alerts go to the room's occupant and escalate to the warden if ignored, with wasted kWh tracked at room, floor, block and building level.]
- [An offline open-weight AI writes clear alerts and answers questions like "Why is C305 flagged?", and the system can later switch from synthetic data to real room sensors without redesign.]

## Innovation and Differentiation

[CampusWatch ties electricity use to who is actually in each room, and then to who is responsible for it. It does this in a 3D campus you can walk down from building to block, floor and room, with an offline open-weight model that explains what it finds.

How it differs from conventional solutions

Conventional: Energy tracking is usually per building or per feeder, shown as monthly bills or charts, so waste is only noticed after the fact and nobody knows which room caused it. CampusWatch: It works at room level in near real time and flags the exact room, such as C305.
Conventional: Dashboards and building management systems show consumption but don't know whether anyone is in the room, so they can't tell "AC running while someone sleeps" from "AC running in an empty room". CampusWatch: It compares each room's power with its occupancy and its own normal pattern, so it catches both devices left on in empty rooms and sudden abnormal spikes.
Conventional: Alerts, where they exist, go to a central facilities team, and the person who left the AC on never hears about it. CampusWatch: The alert goes to the room's occupant and escalates to the warden if ignored, so detection leads to action.
Conventional: Analytics tools are often closed, cloud-based and need technical staff to read. CampusWatch: It uses a local open-weight model that writes plain-language alerts and answers questions like "Why is C305 flagged?" without internet, keeping room-level data on campus, and the whole project is open source for any campus to reuse.
The prototype runs on synthetic data in the same format that room sensors (power meters and presence sensors) would send, so moving to live data means changing the data source, not the system.]

## Technical Implementation

### Architecture

[<img width="987" height="687" alt="image" src="https://github.com/user-attachments/assets/1538fb0f-6b5f-4d0c-b84e-457e1e81e6a0" />
]

### Technology Stack


| Category        | Technologies                           |
| --------------- | ---------------------------------------|
| Frontend        | [HTML/CSS , JavaScript , Three.js]     |
| Backend         | [Fast API , Pydantic , Ollama]         |
| Database        | [SQLite]                               |
| AI / ML         | [Open-weight LLM]                      |
| Infrastructure  | [Uvicorn]        |
| APIs / Services | [REST +JSON]            |


If a category or technology is not implemented in the project, specify `N/A` instead of leaving the field blank.

### How It Works

[CampusWatch has six major components, and data flows through them in one direction, from raw readings to an alert in front of a person.

Data source. In the prototype, a seeded generator produces room-level power readings, occupancy and 7 days of history. With real sensors, power meters, presence sensors and door contacts send the same data format over MQTT. Everything downstream is identical either way.
State engine (backend). The FastAPI backend receives these readings on a simulated clock and keeps each room's current state: live kW, expected kW, who is present, and which devices are drawing power. It also rolls the numbers up to floor, block and building level, along with wasted kWh.
Detector. Every time slot, it compares each room's consumption with its occupancy and its own normal pattern. It raises an alert when a room has been empty for 20 minutes with devices still on, when power spikes far above normal, or when it stays too high for an hour.
Alert manager. It gives each alert a severity, prevents duplicates, applies a cooldown, and sends it to the room's occupant (or the in-charge for an academic room). If nobody acknowledges within 30 minutes, it escalates to the warden.
Open-weight AI (Ollama + Qwen2.5-7B). The alert manager passes it only structured facts, such as the room, the device, the duration and the kWh wasted. It returns a plain-language message. It also explains why a room was flagged and answers questions from the 3D interface using only those supplied numbers. If the model is offline, a template writes the message instead.
3D web interface (Three.js). It loads your Blender campus model and polls the REST API. It colors the three interactive buildings by status, lets you drill down from block to floor to room, shows the room panel with power against expected, and displays alerts in the alert center and each person's inbox.

How they interact: a reading enters through the data source, the state engine updates the room, and the detector checks it. If something is wrong, the alert manager creates an alert, asks the AI for the wording, and delivers it. The interface shows the new status and alert on the next update. A user can also click a room and ask the AI why it was flagged, and that request goes to the model with the room's data. The detector decides when something is wrong, and the AI only words the alert, so the numbers always come from code.]

### Technical Decisions

[Architecture

Detection is code, wording is the model. Deterministic rules decide when something is wrong, and the open-weight model only receives structured facts and writes the message. Every number can be traced and tested, and the model cannot invent a reading or a kWh figure.
One data format for synthetic and real data. The simulator emits the same records that room power meters and presence sensors would send. Moving to live sensors changes only the data source, and the simulator stays as the demo and fallback mode.
Offline-first. The model runs locally through Ollama and all frontend libraries are vendored. Room-level data stays on the campus, the system keeps working without internet, and the open-source requirement is met.
Configuration instead of hard-coding. Buildings, blocks, floors and the room-numbering rule (block letter + floor + two digits, like C305) live in a config file, and a mapping file links building names to the meshes in the Blender model. Only the three buildings with known room data are interactive, and any campus can plug in its own layout.
Floors and rooms are built in code. Only approximate room sizes are known, so the 3D floors and rooms are generated procedurally in Three.js. This keeps the model light, editable, and consistent with the config.
Algorithms

A learned "normal", not a fixed threshold. A room's expected power depends on who is present and the time of day, so each room is compared with an expected-kW model and its own rolling 7-day same-time average. This avoids false alarms like treating a sleeping occupant's AC as waste.
Three rules for three failure types. Devices left on in an empty room, a sudden spike (more than 1.6 times expected for two slots, or a z-score above 3), and sustained overuse (25% above expected for an hour). Each rule needs persistence, such as 20 minutes of emptiness, so normal noise of about ±15% does not trigger alerts.
Alert fatigue control. Duplicates are merged, a 30-minute cooldown prevents repeats, severity is based on kWh wasted and duration, and unacknowledged alerts escalate to the warden.
Statistical rules over a trained model. There is no labelled fault data, and rules are explainable to the person receiving the alert. An Isolation Forest can be added later once real sensor history exists.
Engineering

Validated model output with fallbacks. The model returns schema-constrained JSON, checked with Pydantic and retried up to three times, and a template writes the message if the model is offline. A model failure never stops an alert.
Reproducibility and testing. Data is seeded so runs are deterministic. Tests check that injected incidents are caught within about 25 minutes, that normal noise produces under 2% false alarms over 7 days, and that room IDs follow the rule.
A simulated clock with incident injection. Time can be sped up and incidents injected on demand, which lets us test and demonstrate the whole alert flow in seconds.
Privacy by design. Occupants are synthetic pseudonymous IDs, and the sensor roadmap uses presence and power sensors, not cameras.]

## Implementation During the Hackathon

[During the Hack Day, we built CampusWatch, a working prototype that monitors room-level electricity use on a 3D model of our campus. We created a seeded synthetic dataset of room power, occupancy and 7-day history, and a rule-based detector that flags devices left on in empty rooms, sudden abnormal spikes and sustained overuse. An alert manager removes duplicates, applies a cooldown, notifies the room's occupant and escalates to the warden if the alert is ignored. We connected an offline open-weight model (Qwen2.5-7B through Ollama) that writes the alerts, explains why a room was flagged and answers questions, with a template fallback if the model is unavailable. The web interface loads our Blender campus model and lets users drill down from Kashyapa Bhavanam, AB1 or Maithreyi Bhavanam to blocks, floors and rooms, each colored by live status, with a room panel, an alert center and a "view as resident" inbox. We backed it with a FastAPI backend, automated tests, an Agent Skill (SKILL.md), a README and an open-source license, and published everything in a public GitHub repository with a demo video. The prototype runs on synthetic data in the same format real room sensors would send, so live sensor integration is the planned next step.]

### Team Contributions

- **[Prajit Anand]:** [Prototype Build]
- **[Sriram S]:** [Prototype Build]
- **[Mohit Vaswani]:** [Research and Development]
- **[Tanishq Borah]:** [Research and Development]

## Working Application

**Live Application:** [https://campuswatch-amrita.aryansharma0680.chatgpt.site/]

[3D drill-down. Hover Kashyapa Bhavanam, AB1 or Maithreyi Bhavanam, pick a block, click a floor, then hover and click a room such as C305. Other buildings stay non-interactive.
Live status. Press play on the clock bar and speed it up. Rooms change color (green, amber, red) as power and occupancy change.
Incident injection. Use the demo control, or POST /api/inject, to leave a room's AC on after it empties or to create a spike. The detector raises an alert within minutes of simulated time.
Alerts and escalation. Open the bell to see the alert with its device, duration and wasted kWh. Acknowledge or resolve it, or leave it open for 30 simulated minutes to see it escalate to the warden. Use "View as resident" to see what the occupant receives.
AI assistant. Click a flagged room and choose "Ask AI why", or ask "Which block wasted the most power today?"
Wasted-kWh totals at room, floor, block and building level.]

The submitted application should be functional and accessible through the provided link where applicable.

## Demo Video

**Demo Video:** [https://youtu.be/f6CFN6bZwnQ?si=US6BhiiG47HWWack]

[Demo walkthrough

1. The problem
Show the 3D campus loading. Say: "ACs, fans and lights get left on in empty rooms all over campus, and nobody knows which room or who is responsible. CampusWatch makes it visible."

2. 3D drill-down
Hover Kashyapa Bhavanam, which glows while the other buildings stay grey. Pick Block C from the popover, click Floor 3, and show the 12 rooms (C301 to C312) colored green, amber or red. Hover C305 to show its occupants, live kW against expected, and the devices on.

3. Detection
Press play on the clock bar and speed it up. Inject a "left-on" incident in C305. Show the occupants leaving, the AC still drawing power, and the room turning red after the 20-minute empty window. Say: "The detector compares each room's power with its occupancy and its own normal pattern."

4. Alert and escalation
Show the toast and open the alert center: room, device, duration and kWh wasted. Open "View as resident" to show the message the occupant receives, written by the offline open-weight model. Leave it unacknowledged and show it escalating to the warden.

5. Spike detection
Inject a sudden spike in a different room. Show it flagged as abnormal, and mention that ordinary variation of about ±15% does not trigger anything.

6. AI assistant
Click a flagged room, choose "Ask AI why", and read the explanation. Then ask "Which block wasted the most power today?" and show the answer. Mention it runs locally through Ollama, with no internet.

7. Close
Show the wasted-kWh totals at floor, block and building level. Say: "Synthetic data today, real room sensors next. Open source on GitHub."

Before you record, run the clock at its fastest speed so each step finishes quickly. Do a dry run with the injected incidents prepared, and keep the "Synthetic data" badge visible.]

## Open Source and AI Usage

### AI / Models

- **[ChatGPT]:** [To Build Prototype]

### Open Source Components

- **[Library / Framework]:** [Purpose]
- **[Dataset]:** [Purpose]
- **[API / Service]:** [Purpose]

[Include relevant licenses, attribution, and acknowledgements for external components.]

## Setup and Usage

### Prerequisites

- [Requirement]
- [Requirement]

### Installation

```bash
git clone [repository-url]
cd [project-directory]
[installation-command]
```

### Environment Variables

```env
[VARIABLE_NAME]=[value]
```



### Running the Project

```bash
[run-command]
```

### Usage

[Explain the basic steps required to use the project.]

## Devpost Submission

**Devpost Project:** [[Devpost Project URL](https://dev.to/mvaswani0002/hacktober-fest-1o6m)]

[Add the link to the team's Devpost submission. Ensure the Devpost project page is complete and contains the required project information, links, media, and team details.]

## Credits and License

### Credits

[Credit libraries, frameworks, datasets, models, APIs, contributors, and other external resources used.]

### License

[License name and/or link.]

## Submission Checklist

- [ ] Project title and description added
- [ ] All team members listed
- [ ] Problem clearly explained
- [ ] Reason for choosing the problem explained
- [ ] Solution and key features documented
- [ ] Innovation and differentiation explained
- [ ] Architecture included
- [ ] Technical implementation documented
- [ ] Work completed during the hackathon documented
- [ ] Team contributions documented
- [ ] Working application is functional
- [ ] Live application link added where applicable
- [ ] Demo video added
- [ ] AI and open-source components documented
- [ ] Setup and usage instructions tested
- [ ] Challenges and learnings documented
- [ ] Devpost submission completed
- [ ] Devpost link added
- [ ] Credits added
- [ ] License added
- [ ] Repository is organized and complete
