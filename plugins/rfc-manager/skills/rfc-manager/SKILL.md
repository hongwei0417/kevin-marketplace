---
name: rfc-manager
description: Manage RFC (Request for Comments) lifecycle for the Moxa frontend team's GitLab project. Use when creating RFC proposals, managing RFC discussions and voting, archiving RFC decisions, or tracking implementation status. Triggers on mentions of "RFC", "proposal", "team decision", "one-rfcs", "voting", or "archive RFC".
---

# RFC Manager

## Overview

Manage the complete RFC (Request for Comments) lifecycle for the Moxa frontend team's one-rfcs GitLab project (moxa/sw/f2e/one/one-rfcs). This skill provides structured workflows for proposing technical decisions, facilitating team discussions, managing voting processes, archiving decisions, and tracking implementation.

## When to Use This Skill

Use this skill when:
- Creating a new RFC proposal in GitLab Issues
- Managing RFC discussion and review processes
- Conducting RFC voting and decision-making
- Archiving accepted or rejected RFCs
- Tracking implementation status of approved RFCs
- Querying RFC status, labels, or workflow steps

**Project Information:**
- GitLab Project: `moxa/sw/f2e/one/one-rfcs`
- RFC discussions happen in GitLab Issues
- RFC archives are stored in `archived/accepted/` or `archived/rejected/`

## Core Workflow

The RFC process follows a structured lifecycle from proposal to implementation. Each phase has specific actions and GitLab operations.

### Phase 1: Creating RFC Proposals

When creating a new RFC proposal:

1. **Prepare RFC Content**
   - Use the RFC proposal template from [templates/rfc-proposal-template.md](templates/rfc-proposal-template.md)
   - Fill all required sections (marked in template):
     - RFC information (proposer, participants, date)
     - Problem/motivation description
     - Solution approach
     - Impact assessment
     - Risk assessment
   - Optionally fill alternatives, implementation plan, references

2. **Create GitLab Issue**
   - Use `mcp__gitlab__create_issue` with:
     - `project_id`: "moxa/sw/f2e/one/one-rfcs"
     - `title`: "RFC-{IssueID}: [Brief Description]"
     - `description`: Populated from template
     - `issue_type`: "issue"
   - After creation, update title with actual Issue ID

3. **Add Labels and Assignees**
   - Apply appropriate labels using `mcp__gitlab__update_issue`:
     - Process category: `process`
     - Product category: `products`
     - Working group: `wg/dev-rel`, `wg/ever-green`, `wg/infra`, `wg/ui-library`, `wg/vizion`
   - Assign relevant team members using `@username` mentions

4. **Set Timeline**
   - Use `mcp__gitlab__update_issue` to set:
     - `start_date`: Discussion start date
     - `due_date`: Discussion end date (2-3 weeks, max 1 month)
   - Update status from "To Do" to "In progress"

**Reference:** For detailed label usage, see [references/labels-guide.md](references/labels-guide.md)

### Phase 2: Managing Discussion and Review

During the discussion phase:

1. **Monitor Discussion**
   - Use `mcp__gitlab__list_issue_discussions` to retrieve comments and threads
   - Track key questions, concerns, and feedback
   - Identify consensus points and disagreements

2. **Facilitate Review**
   - Help proposer respond to questions
   - Summarize discussion points
   - Suggest modifications based on feedback
   - Manage back-and-forth iterations

3. **Prepare for Voting**
   - Confirm all required sections are complete
   - Ensure key participants have reviewed
   - Verify discussion period is complete

**Reference:** For complete RFC workflow, see [references/rfc-workflow.md](references/rfc-workflow.md)

### Phase 3: Conducting Voting and Decision

After discussion period ends:

1. **Initiate Voting**
   - Voting period: 48 hours after discussion ends
   - Voting method: Emoji reactions on Issue
     - 👍 = Approve
     - 👎 = Reject
     - 👀 = Abstain
   - Eligible voters: Key participants mentioned in RFC

2. **Count Votes**
   - Retrieve vote counts from Issue reactions
   - Calculate approval rate
   - Decision rule: >50% approval to pass

3. **Record Decision**
   - Use `mcp__gitlab__create_issue_note` to post:
     - Final vote tally
     - Decision outcome (Accepted/Rejected)
     - Key decision rationale
     - Next steps

**Reference:** For voting and closing process, see [references/rfc-workflow.md](references/rfc-workflow.md) section "RFC 結案流程"

### Phase 4: Archiving RFC Decisions

After voting decision:

1. **Create Archive Branch**
   - Use `mcp__gitlab__create_branch` to create branch from Issue
   - Branch name format: `rfc-{issue-id}-archive`

2. **Prepare Archive Document**
   - Use template from [templates/rfc-archive-template.md](templates/rfc-archive-template.md)
   - Fill in:
     - RFC number (Issue ID)
     - Final status (Accepted/Rejected)
     - Decision date
     - Participants
     - Decision outcome
     - Implementation plan (if accepted)
   - File name format: `{issue-id}-brief-description.md`

3. **Choose Archive Location**
   - Accepted RFCs: `archived/accepted/{issue-id}-brief-description.md`
   - Rejected RFCs: `archived/rejected/{issue-id}-brief-description.md`

4. **Create Merge Request**
   - Use `mcp__gitlab__create_merge_request`:
     - `source_branch`: RFC archive branch
     - `target_branch`: "main"
     - `title`: "[RFC-{Issue ID}] 歸檔 RFC - [Brief Description]"
     - `description`: Link to original Issue
   - Request approval from key participants
   - Merge after approval

5. **Update Issue Status**
   - After MR merge, use `mcp__gitlab__update_issue`:
     - For accepted: Add label `status/accepted`, state "Done"
     - For rejected: Add label `status/rejected`, state "Won't Do"
   - Add archive document link to Issue
   - Close Issue

**Important:** Archive MR must be merged before closing Issue

### Phase 5: Tracking Implementation (Accepted RFCs Only)

After archiving accepted RFCs:

1. **Link Implementation Tracking**
   - Create Jira Epic for implementation
   - Link Epic to GitLab RFC Issue
   - Reference archived RFC document in Epic description

2. **Monitor Progress**
   - Track implementation milestones
   - Report blockers or changes needed
   - If RFC needs modification, create new RFC Issue

**Note:** Implementation happens after Issue closure. Do not reopen closed Issues for implementation discussions.

## GitLab MCP Integration

This skill relies heavily on GitLab MCP tools. Key operations:

**Issue Operations:**
- `mcp__gitlab__create_issue` - Create RFC proposals
- `mcp__gitlab__get_issue` - Retrieve RFC details
- `mcp__gitlab__update_issue` - Update labels, status, dates
- `mcp__gitlab__list_issues` - Search RFCs by label, status
- `mcp__gitlab__list_issue_discussions` - Read discussions

**Note Operations:**
- `mcp__gitlab__create_issue_note` - Add comments to RFC
- `mcp__gitlab__get_issue_notes` - Retrieve all comments

**Merge Request Operations:**
- `mcp__gitlab__create_merge_request` - Submit archive
- `mcp__gitlab__get_merge_request` - Check MR status
- `mcp__gitlab__update_merge_request` - Update MR details

**Branch Operations:**
- `mcp__gitlab__create_branch` - Create archive branch

**File Operations:**
- `mcp__gitlab__create_or_update_file` - Add archive file
- `mcp__gitlab__get_file_contents` - Read existing files

**Common Patterns:**
```
# Get RFC Issue
mcp__gitlab__get_issue(
  project_id="moxa/sw/f2e/one/one-rfcs",
  issue_iid="{issue_id}"
)

# Update RFC with labels
mcp__gitlab__update_issue(
  project_id="moxa/sw/f2e/one/one-rfcs",
  issue_iid="{issue_id}",
  labels=["process", "wg/infra"]
)

# Create archive MR
mcp__gitlab__create_merge_request(
  project_id="moxa/sw/f2e/one/one-rfcs",
  source_branch="rfc-{issue_id}-archive",
  target_branch="main",
  title="[RFC-{issue_id}] 歸檔 RFC - {description}"
)
```

## Resources

### references/
Contains detailed RFC workflow documentation:
- [references/rfc-workflow.md](references/rfc-workflow.md) - Complete RFC process with all phases
- [references/labels-guide.md](references/labels-guide.md) - Label usage and categorization rules

Load these references when:
- User asks about specific workflow steps
- Clarification needed on voting rules
- Label selection guidance required
- Archive process details needed

### templates/
Contains templates for RFC documents:
- [templates/rfc-proposal-template.md](templates/rfc-proposal-template.md) - Template for creating new RFC Issues
- [templates/rfc-archive-template.md](templates/rfc-archive-template.md) - Template for archiving RFC decisions

Use these templates to:
- Generate RFC Issue descriptions
- Create standardized archive documents
- Ensure all required sections are included

## Quick Reference

**RFC Lifecycle Stages:**
1. Proposal → 2. Discussion → 3. Voting → 4. Archive → 5. Implementation

**Key Labels:**
- Status: `status/accepted`, `status/rejected`
- Category: `process`, `products`
- Working Groups: `wg/dev-rel`, `wg/ever-green`, `wg/infra`, `wg/ui-library`, `wg/vizion`

**Important Timing:**
- Discussion period: 2-3 weeks (max 1 month)
- Voting period: 48 hours after discussion ends
- Archive MR merge: Required before closing Issue

**GitLab Project:** moxa/sw/f2e/one/one-rfcs
