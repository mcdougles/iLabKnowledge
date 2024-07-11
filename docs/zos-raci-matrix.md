This matrix defines the roles and responsibilities regarding zOS pipeline support.

??? question "What does RACI stand for?"
    - Responsible: People or stakeholders who do the work. They must complete the task or objective or make the decision. Several people can be jointly Responsible.
    - Accountable: Person or stakeholder who is the "owner" of the work. He or she must sign off or approve when the task, objective, or decision is complete. This person must make sure that responsibilities are assigned in the matrix for all related activities. Success requires that there is only one person Accountable, which means that "the buck stops there."
    - Consulted: People or stakeholders who need to give input before the work can be done and signed off on. These people are "in the loop" and active participants.
    - Informed: People or stakeholders who need to be kept "in the picture." They need updates on progress or decisions, but they do not need to be formally consulted, nor do they contribute directly to the task or decision.

    [Source](https://www.cio.com/article/287088/project-management-how-to-design-a-successful-raci-project-plan.html)


??? question "Who are the stakeholders?"
    - App Team: These are users of the zos-application pipelines
    - z/OS Platform team: This is the team that manages the environment that the Pipeline executes on
    - DevEx Team: This is a combination of zModernization team and the CIO CI/CD Pipeline Team

Typically the CI/CD Pipeline Team will be the primary point of contact for triage.

|            Component / Task             | z/OS Platform team | DevEx Team | App Team | Points of Contact                                       | Notes / Justification                                                                                                                                                 |
| :-------------------------------------: | :----------------: | :--------: | :------: | ------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|             Bug in Pipeline             |                    |     RA     |    I     | [Critical Incident Support](#critical-incident-support) | The pipeline team may need to consult with the zMod; may or may not be solved by Pipeline Team solely.                                                                |
|        Developer Portal is down         |                    |     RA     |    I     | [Critical Incident Support](#critical-incident-support) | The developer portal (including the definition of the end points that allow triggering of the release management pipeline) will prevent the ability of deploying apps |
|    z/OS Platform team Platform issue    |         R          |     A      |    I     | Service Now Tickets (Created by App Team)               | Pipeline tools aren't mounted (or deployed), networking issue, storage issue, etc                                                                                     |
| Creation of tools/configs on Cirrus 1.0 |         CI         |     RA     |          | Service Now Tickets (Created by DevEx Team)             | The zMod team will create the configs for the pipeline to use, the platform team may need to be involved as a consult in certain situations                           |

## Critical Incident Support
If an issue with the CIO z/OS pipeline prevents you from deploying a change to resolve a priority 1 or priority 2 incident in your application (as defined by CIO Service Management), [create a new incident](https://ibm.service-now.com/now/sow/record/incident/-1_uid_3) on the pipeline application (Assignment group: CIO CI/CD, Category: Application, Configuration Item: CIO CI/CD Pipelines - Production) and also report it on the channel #dx-platform-support