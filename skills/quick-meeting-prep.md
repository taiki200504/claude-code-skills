---
name: quick-meeting-prep
description: Generate a 1-page meeting prep sheet with agenda, talking points, and questions from calendar events or manual input
---

# Quick Meeting Prep

Pull calendar event details, attendee info, and relevant context to generate a concise prep sheet before any meeting. Works with Google Calendar MCP or manual input.

## When to Use

- Before any scheduled meeting (client call, standup, 1-on-1, investor pitch)
- When you need to quickly understand who you're meeting with and what to discuss
- When preparing talking points for a meeting you were just invited to

## How It Works

### Step 1: Gather Meeting Details

**If Google Calendar MCP is available:**
1. Use `gcal_list_events` to find the upcoming meeting by time or title
2. Extract: title, time, duration, attendees (names + emails), description, location/link

**If no calendar MCP:**
1. Ask the user for: meeting title, attendees, time, and any context
2. Accept natural language input like "meeting with Tanaka-san from ABC Corp at 2pm about partnership"

### Step 2: Research Attendees

For each attendee:
1. Search for their role/company if not already known
2. Check recent email threads (if Gmail MCP available) for prior communication
3. Note any previous meeting history or shared context

If attendee research tools are unavailable, skip this step and note it in the output.

### Step 3: Compile Context

1. Check for any relevant documents, PRDs, or notes mentioned in the meeting description
2. If the meeting is recurring, look at notes from the last occurrence
3. Identify any open action items related to the meeting topic

### Step 4: Generate Prep Sheet

Output a structured prep sheet in this format:

```markdown
# Meeting Prep: [Title]
**Date:** [Date & Time] | **Duration:** [Duration] | **Location:** [Link/Room]

## Attendees
| Name | Role | Notes |
|------|------|-------|
| [Name] | [Role @ Company] | [Relevant context] |

## Agenda
1. [Topic 1] (estimated time)
2. [Topic 2] (estimated time)
3. Open discussion / Q&A

## Key Talking Points
- [Point 1 with supporting context]
- [Point 2 with supporting context]
- [Point 3 with supporting context]

## Questions to Ask
- [Strategic question 1]
- [Clarifying question 2]
- [Follow-up question 3]

## Prep Checklist
- [ ] [Any materials to prepare]
- [ ] [Any decisions needed before the meeting]
```

### Error Handling

- If calendar MCP is unavailable, prompt the user for manual input immediately
- If attendee info cannot be found, mark as "Info not available" rather than guessing
- If the meeting has no description, generate agenda based on the title and attendee roles
- Never fabricate attendee details or company information

## Example

**User input:**
```
Prep me for my 2pm meeting with Sato-san from SoftBank about the EGG demo
```

**Output:**
```markdown
# Meeting Prep: EGG Demo - SoftBank
**Date:** 2026-04-11 14:00 JST | **Duration:** 60min | **Location:** Google Meet

## Attendees
| Name | Role | Notes |
|------|------|-------|
| Sato-san | BD Manager @ SoftBank | First demo meeting |
| Taiki Mishima | CEO @ Gugen | Presenting |

## Agenda
1. Company & product introduction (10min)
2. EGG live demo (20min)
3. B2B pricing & deployment model (15min)
4. Q&A and next steps (15min)

## Key Talking Points
- EGG's unique value: immersive meditation pod with biometric feedback
- B2B SaaS model: Y30K/month/unit, includes software updates
- Target: corporate wellness programs, co-working spaces

## Questions to Ask
- What is SoftBank's current employee wellness budget?
- Are there existing wellness vendors we'd need to integrate with?
- What's the decision-making timeline for new vendor onboarding?

## Prep Checklist
- [ ] EGG demo unit charged and tested
- [ ] One-pager PDF ready to share
- [ ] Pricing sheet for enterprise tiers
```
