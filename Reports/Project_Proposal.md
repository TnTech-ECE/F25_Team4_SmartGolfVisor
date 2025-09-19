# Project Proposal

[//]: # "This document provides a comprehensive explanation of what a project proposal should encompass. The content here is detailed and is intended to highlight the guiding principles rather than merely listing expectations. The sections that follow contain all the necessary information to understand the requirements for creating a project proposal."


[//]: # " General Requirements for the Document
- All submissions must be composed in markdown format.
- All sources must be cited unless the information is common knowledge for the target audience.
- The document must be written in third person.
- The document must identify all stakeholders including the instuctor, supervisor, and cusotmer.
- The problem must be clearly defined using "shall" statements.
- Existing solutions or technologies that enable novel solutions must be identified.
- Success criteria must be explicitly stated.
- An estimate of required skills, costs, and time to implement the solution must be provided.
- The document must explain how the customer will benefit from the solution.
- Broader implications, including ethical considerations and responsibilities as engineers, must be explored.
- A list of references must be included.
- A statement detailing the contributions of each team member must be provided."


## Introduction
[//]: # "The introduction must be the opening section of the proposal. It acts as the elevator pitch of the project, briefly introducing the objective, its importance, and the proposed solution. Because readers may only read this section, it should effectively capture their attention and encourage them to read further.
Toward the end of the introduction, include a subsection that outlines what the proposal will cover. This helps set reader expectations for the ensuing sections."

Golfers often make decisions on the course with incomplete information: they may not know exactly how far their shots carried, at what speed the ball left the club, or precisely where the ball landed. The **Smart Golf Visor** is a wearable, real-time feedback system designed to deliver key shot metrics (distance, launch angle, ball speed, etc.) along with a visual (or non-visual) indication of shot trajectory, right when the shot is made. The system incorporates an affordable launch monitor, a display interface (such as AR glasses / a heads-up display), and a camera based shot tracer.  

**Inclusive / Accessibility Extension:** For users who are visually impaired or have low vision, the Smart Golf Visor offers additional feedback modes — audible announcements (e.g., “shot landed 150 yards to your right”), vibration or tone cues, or high-contrast large text. This allows these users to independently track where the ball went, how far, and makes the game more accessible, less frustrating, and more empowering.

### Proposal Outline

This proposal will cover the following:

1. **Background & Motivation** — Prior work in golf analytics, shot feedback systems, and assistive sports technology; gaps this project addresses (especially for visually impaired users).  
2. **Design & Technical Approach** — Hardware components, software architecture, display and feedback modes (visual / non-visual), power management.  
3. **Implementation Plan** — Timeline, prototypes, testing (including user testing with visually impaired / low vision participants), team roles.  
4. **Evaluation** — How we will measure success: accuracy of shot metrics, latency of feedback, battery life, user satisfaction (including visually impaired users), reliability in different lighting / environmental conditions.  
5. **Budget & Resources** — Detailed cost breakdown, existing resources, where we plan to spend, possible trade-offs.  
6. **Broader Implications, Ethics, & Responsibility** — Global, economic, environmental, societal impacts; ethical issues (privacy, fairness, accessibility); mitigation strategies; responsibilities of engineering team.

## Formulating the Problem

Formulating the problem or objective involves clearly defining it through background information, specifications, and constraints. Think of it as "fencing in" the objective to make it unambiguously clear what is and is not being addressed and why.

Questions to consider:
- Who does the problem affect (i.e. who is your customer)?
- Why do we need this solution?
- What challenges necessitate a dedicated, multi-person engineering team?
- Why aren’t off-the-shelf solutions sufficient?

### Background

Provide context and details necessary to define the problem clearly and delineate its boundaries.

### Specifications and Constraints

Specifications and constraints define the system's requirements. They can be positive (do this) or negative (don't do that). They can be mandatory (shall or must) or optional (may). They can cover performance, accuracy, interfaces, or limitations. Regardless of their origin, they must be unambiguous and impose measurable requirements.

#### Specifications

Specifications are requirements imposed by **stakeholders** to meet their needs. If a specification seems unattainable, it is necessary to discuss and negotiate with the stakeholders.

#### Constraints

Constraints often stem from governing bodies, standards organizations, and broader considerations beyond the requirements set by stakeholders.

Questions to consider:
- Do governing bodies regulate the solution in any way?
- Are there industrial standards that need to be considered and followed?
- What impact will the engineering, manufacturing, or final product have on public health, safety, and welfare?
- Are there global, cultural, social, environmental, or economic factors that must be considered?


## Survey of Existing Solutions

Research existing solutions, whether in literature, on the market, or within the industry. Present these findings in a coherent, organized manner. Remember to cite all information that is not common knowledge.


## Measures of Success

Define how the project’s success will be measured. This involves explaining the experiments and methodologies to verify that the system meets its specifications and constraints.


## Resources

Each project proposal must include a comprehensive description of the necessary resources.

### Budget

Provide a budget proposal with justifications for expenses such as software, equipment, components, testing machinery, and prototyping costs. This should be an estimate, not a detailed bill of materials.

### Personel

This team consists of six electrical engineering students who all share some similar skills but also possesses unique skills from one another.

Brandon Zerkovich was chosen to be the leader of this team and his responsibilities include delegating tasks amongst the team and making sure that deadlines will be met by each individual. Brandon skills include C and C++ programming languages and power system analysis. These skills will help with the microcontroller and the battery system that will be used for this project.

Landen Johnson...

Ryan Zerkovich...

Andrew Goostree...

Bryce Hughes...

Michael Madigan...

While many of the skills in this project is met by at least one team member, there are some skills gaps noticed within the team. One skill gap is digital signal processing which will be used with tracking the ball from the camera system. In for this project to be successful, the team will learn this...

The supervisor that was chosen for this project is J.C. Williams. J.C. Williams has done extensive work with radar technologies, digital signal processing, image signal processing, and computer vision.

### Timeline

Provide a detailed timeline, including all major deadlines and tasks. This should be illustrated with a professional Gantt chart.


## Specific Implications

Explain the implications of solving the problem for the customer. After reading this section, the reader should understand the tangible benefits and the worthiness of the proposed work.


## Broader Implications, Ethics, and Responsibility as Engineers

[//]: # "Consider the project’s broader impacts in global, economic, environmental, and societal contexts. Identify potential negative impacts and propose mitigation strategies. Detail the ethical considerations and responsibilities each team member bears as an engineer."
### Global, Economic, Environmental, Societal Impacts

- **Global / Economic Impacts:**  
  - Potential to create an inclusive sports tool usable worldwide. Could be used as a cheaper training tool for those without large incomes.  
  - Could lead to small business or startup opportunities in sports/assistive tech.  


- **Environmental Impacts:**  
  - **Electronic waste concerns:** batteries, displays, sensors may degrade or become obsolete overtime.  
  - **Energy consumption:** devices that draw high power degrade batteries faster and will need more frequent charging.  
  - **Materials:** sourcing, manufacturing of electronics has environmental footprint. Choosing durable, repairable, and recyclable components reduces impact.

- **Societal Impacts:**  
  - **Accessibility & Inclusion:** Makes golf more accessible to visually impaired or low vision individuals, helping them enjoy the sport more fully and independently.  
  - **Education:** Opportunity for STEM learning; engineering students gain experience building inclusive technology.  


### Ethical Considerations and Mitigation Strategies

| **Ethical / Responsible-Engineering Issue** | **Potential Negative Impact** | **Mitigation Strategy** |
|---|---|---|
| **Privacy & Data Consent** | Recording video may inadvertently capture bystanders; user may not want location / performance data stored or shared. | Design system so data remains local; try to encrypt stored data.|
| **Bias, Reliability, Fairness** | Computer vision or sensors may perform poorly in low light, glare, or certain clothing; can mis-locate shot or mis-estimate distance. | Extensive testing in different settings; build error margins / confidence levels; user feedback loop. |
| **Accessibility & Usability** | If the device is cumbersome, heavy, or non-intuitive,small displays hard to read. | Large/high-contrast text; ergonomic design; make controls simple; ensure display does not block too much field of view. |
| **Safety & Distraction** | Heads-up displays or wearable screens could distract users | Limit visual overlays; allow user to pause/disable feedback. |
| **Environmental Footprint** | Frequent replacement of batteries / components; disposables; poor lifecycle planning. | Use rechargeable batteries; design modular systems so parts can be replaced; pick durable hardware. |

### Responsibilities of Engineering Team

- Be transparent about capabilities and limitations  
- Respect user privacy and autonomy in data collection, storage, sharing.  
- Strive for environmental sustainability: choose responsible hardware, consider lifespan and repairability.  
- Maintain safety, usability, and ethical integrity
---


## References

[//]: # "All sources used in the project proposal that are not common knowledge must be cited. Multiple references are required."

[1] IEEE, “IEEE code of ethics | IEEE,” IEEE Advancing Technology for Humanity, https://www.ieee.org/about/corporate/governance/p7-8 


## Statement of Contributions

Each team member must contribute meaningfully to the project proposal. In this section, each team member is required to document their individual contributions to the report. One team member may not record another member's contributions on their behalf. By submitting, the team certifies that each member's statement of contributions is accurate.
