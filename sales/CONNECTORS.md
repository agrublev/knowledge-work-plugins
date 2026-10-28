# Connectors

## How tool references work

Plugins are **tool-agnostic**. They describe workflows in terms of categories (CRM, chat, email, etc.) rather than specific products. The `.mcp.json` pre-configures specific MCP servers, but any MCP server in that category works.

Many plugins use `~~category` placeholders in their files. The skills in this plugin name the category in plain words instead ("the CRM", "email", "the calendar", "call transcripts"). Either way, a skill refers to the category, so any connector in that category works. The placeholder column below maps each category to the shared naming used across plugins.

Every skill also works with nothing connected: upload an export or paste notes, and the skill works from that.

## Connectors for this plugin

| Category | Placeholder | Included servers | Other options |
|----------|-------------|-----------------|---------------|
| CRM | `~~CRM` | HubSpot, Salesforce\*, Close, Monday, Freedcamp (CRM) | Pipedrive, Attio, Zoho CRM, Copper |
| Email | `~~email` | Gmail, Microsoft 365 (Outlook)\* |  |
| Calendar | `~~calendar` | Google Calendar, Calendly, Microsoft 365 (Outlook)\* |  |
| Chat | `~~chat` | Slack, Microsoft 365 (Teams)\* |  |
| Docs and knowledge base | `~~knowledge base` | Google Drive, Notion, Atlassian (Confluence), Microsoft 365 (SharePoint)\*, Freedcamp (wikis) | Box, Dropbox, Guru |
| Conversation intelligence | `~~conversation intelligence` | Gong, Fireflies, Zoom, Otter.ai | Granola, Fathom, Chorus |
| Data enrichment | `~~data enrichment` | Clay, ZoomInfo, Apollo, Lusha, Crunchbase | Clearbit |
| Sales engagement | `~~sales engagement` | Outreach, Apollo | Salesloft |
| Competitive intelligence | `~~competitive intelligence` | Similarweb | Crayon, Klue |
| Project tracker | `~~project tracker` | Atlassian (Jira), Freedcamp | Linear, Asana |

## Salesforce and Microsoft 365

**Salesforce and Microsoft 365 (marked \* above) are included, and both need setup at the organization level: an admin enables the connector in Claude before people can sign in. For Salesforce, that includes setting up your org's Salesforce app.**

Once connected, the skills use them the same way as any other tool in their category.

Google Calendar, Gmail and Google Drive are listed in `.mcp.json` without a URL. Connect them in Claude's connector settings.

## Permissions

What a connector is allowed to do is set on the connector itself: allow, ask or block for each tool. The skills never go beyond those settings. See the README for recommended settings for scheduled runs.

## Freedcamp

Freedcamp is one connector that covers several categories: tasks, milestones and the Issue Tracker (`~~project tracker`), wikis and discussions (`~~knowledge base`), CRM tasks and call logs (`~~CRM`), plus time tracking, files, calendar and notifications. Its tools are prefixed `fc_`. When a skill refers to one of these categories and Freedcamp is connected, use these tools:

| What the skill needs | Freedcamp tools |
|----------------------|-----------------|
| Resolve projects, groups and apps by name (call once, reuse) | `fc_get_groups_projects` |
| The current user's ID ("me", "my tasks") | `fc_fetch_current_session` or `fc_fetch_current_user` |
| List or search tasks | `fc_fetch_tasks` — omit `project_id` to cover every project; filter with `filters.assigned_to_id`, `filters.status` (`STATUS_NOT_STARTED`, `STATUS_IN_PROGRESS`, `STATUS_REVIEW`, `STATUS_COMPLETED`), `filters.due_date_from` / `due_date_to`, `filters.created_date_from` / `created_date_to` |
| Create a task | `fc_add_task` (`title`, `project_id`, optional `list_id`, `assigned_to_id`, `due_date`, `priority`, `description`), or `fc_add_item_by_names` with a project name |
| Update or complete a task | `fc_update_status` (0 not started, 1 completed, 2 in progress, 3 invalid, 4 review), `fc_update_task`, `fc_batch_edit_tasks` for up to 500 tasks |
| Task lists, sprints, backlogs | `fc_fetch_lists`, `fc_add_list` |
| Milestones and roadmap dates | `fc_fetch_milestones`, `fc_add_milestone`, `fc_edit_milestone`, `fc_fetch_calendar_items` |
| Bugs, tickets and requests | `fc_fetch_issues`, `fc_fetch_issue`, `fc_add_issue`, `fc_edit_issue` (status and type are project-defined; read them off an existing issue first) |
| Comments on any item | `fc_add_comment_by_names`, `fc_add_comment` |
| Project health and activity | `fc_fetch_overview`, `fc_fetch_notifications`, `fc_fetch_notifications_by_project` |
| Wiki pages and docs | `fc_fetch_wikis`, `fc_fetch_wiki`, `fc_add_wiki`, `fc_edit_wiki` (`f_new_version: true` keeps history) |
| Discussions and decisions | `fc_fetch_discussions`, `fc_fetch_discussion`, `fc_add_discussion` |
| CRM follow-ups and call logs | `fc_fetch_crm_tasks`, `fc_add_crm_task`, `fc_fetch_crm_calls`, `fc_add_crm_call` (these take the CRM group's `group_id`, not a project) |
| Time logged and workload | `fc_fetch_times`, `fc_add_time`, `fc_time_action` |
| Link related items across apps | `fc_add_linked_items`, `fc_fetch_linked_items` |

Freedcamp has no full-text search: to find items by keyword, fetch them with the filters above and match titles and descriptions. Freedcamp CRM holds contacts' tasks and calls, not opportunities or pipeline stages. Tools marked destructive (`fc_delete_*`, `fc_leave_project`) are never needed by these skills. Full reference: [mcp.freedcamp.support](https://mcp.freedcamp.support/).
