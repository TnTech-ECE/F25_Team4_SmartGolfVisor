# Smart Golf Visor Project Proposal

## Introduction
[//]: # "The introduction must be the opening section of the proposal. It acts as the elevator pitch of the project, briefly introducing the objective, its importance, and the proposed solution. Because readers may only read this section, it should effectively capture their attention and encourage them to read further.
Toward the end of the introduction, include a subsection that outlines what the proposal will cover. This helps set reader expectations for the ensuing sections."

Golfers often make decisions on the course with incomplete information: they may not know exactly how far their shots carried, at what speed the ball left the club, or precisely where the ball landed. The Smart Golf Visor is a wearable, real-time feedback system designed to deliver key shot metrics (distance, launch angle, ball speed, etc.) along with a visual indication of shot trajectory, right when the shot is made. The system incorporates an affordable launch monitor, a display interface, and a camera based shot tracer.  

**Inclusive / Accessibility Extension:** For users who are visually impaired or have low vision, the Smart Golf Visor offers high-contrast large text. This allows these users to independently track where the ball went, how far, and makes the game more accessible and less frustrating.

### Proposal Outline

This proposal will cover the following:

1. **Background & Motivation** — Prior work in golf analytics, shot feedback systems, and assistive sports technology; gaps this project addresses.  
2. **Design & Technical Approach** — Hardware components, software architecture, display and feedback modes (visual / non-visual), power management.  
3. **Implementation Plan** — Timeline, prototypes, testing, team roles.  
4. **Evaluation** — How we will measure success: accuracy of shot metrics, latency of feedback, battery life, user satisfaction, reliability in different conditions.  
5. **Budget & Resources** — Detailed cost breakdown, existing resources, where we plan to spend, possible trade-offs.  
6. **Broader Implications, Ethics, & Responsibility** — Global, economic, environmental, societal impacts; ethical issues; mitigation strategies; responsibilities of engineering team.

## Formulating the Problem
This section of the project proposal will start off by covering the background information that will detail what the team has found and what they will use to help them make informed decisions for the project. It will then be followed up by the specifications that the team was given by their customer and will end off by discussing the constraints for this project. Both the specifications and constraints will help with narrowing down the design parameters.
### Background

The objective of the Smart Golf Visor is to create a system that is capable of processing information from a launch monitor and an additional camera system, and will display information like ball distance and speed to a visor in real-time. This visor can either be augmented reality glasses, an over-the-head headset, or a see-through wearable eye visor. The system's process will almost entirely be hands-free with the only exception being for the user to set down and pick up the launch monitor. Below is a more detailed background explanation of the components that will be used in this system.

#### *Launch Monitor*
A launch monitor is a device that most commonly uses either a camera-based or a radar-based system to measure crucial data points of a golf swing like ball speed, ball distance, launch angle, etc. 

Launch monitors that use a camera-based system use high-speed cameras that capture images of the club and ball at impact. The amount of images captured can be as low as 2 and as much as 200+[1]. The system then takes those images and compares the data of the launch conditions to a ball-flight model to determine the distance and flight of the ball. The advantage of using this system is that it can take direct measurements of the launch conditions without being interfered by any conditions that may affect the flight of the ball downrange like wind. The accuracy of this monitor comes down to the quantity and quality of the pictures taken. Below is a picture of what a monitor using a camera-based system looks like. 
![image](https://hackmd.io/_uploads/H1wrtkAslx.png)


Launch monitors with a radar-based system work the oppposite way as one with a camera-based system. They use doppler radar to emit radio waves that reflect off the ball and club after impact and gets recieved back to the monitor. This allows for the flight of the ball to be determined after impact. It then uses the flight data by putting it into an algorithm to then calculate and measure the launch conditions that caused that flight[1]. The advantage of this system is that it is able to show where and how far the ball has traveled. The accuracy of this monitor comes down to quality of the radar as well as the correctness of the algorithm. Below is a picture of a monitor that uses a radar-based system.
![image alt](https://hackmd.io/_uploads/SkcY4yRiex.png)
Because of the ability to gather shot information, a launch monitor is a crucial piece of equiptment for this project. Either one of these launch monitor systems can be used for this project.

#### *Display*
The display will be a projection onto a lens that will show the data metrics gathered from the launch monitor. It may possibly also project a tracing of the shot from an additional camera system. The display will come from a transparent, micro-OLED display which is common with near-eye display technology. A transparent OLED display works by generating light pixels on a clear subtrate like glass. The self-emissive microscopic pixels are spaced to permit see-through visibility, balancing image brightness and transparency for a seamless augmented experience[2]. Some advantages that this display has are fast refresh rates, low power consumption, and no backlight[3]. Below is a picture of what the display may look like.
![image](https://hackmd.io/_uploads/rJqsxZRjex.png)
The display is made to be wired to a microcontroller where it will receive its signal to the display for a correct and accurate projection. 

#### *Powering System*
The powering system of the visor will involve a battery that will be used to power all components which will include the microcontroller and display. There are a few options of rechargable batteries that can be used for this system. One option is to use Lithium-ion polymer (LiPo) batteries which are known for their lightweight design. LiPos provide higher specific energies than other lithium batteries, often used in systems where weight is an important factor[4]. They also have a flexible design where they can be bought in a variety of shapes and sizes which allows for more options on battery placement in the visor.
![image](https://hackmd.io/_uploads/S1bBZl0oex.png)

The other option of battery that can be used is a Lithium-ion coin cell battery. These batteries are known to have a longer lifespan than LiPo batteries which would require for less frequent replacements but they do not have a flexible design like LiPos.
![image](https://hackmd.io/_uploads/ByEUSlAjxx.png)
The use of rechargable batteries for this powering system will require a specialized charging circuit so that constant current and voltage will be supplied and ensure a safe charge. The use of non-rechargable batteries is also an option and would eliminate the requirement for a specialized charging circuit. This would require the individual to have to carry spare batteries for the event of a dead battery.

### Specifications and Constraints

Specifications and constraints define the system's requirements. They can be positive (do this) or negative (don't do that). They can be mandatory (shall or must) or optional (may). They can cover performance, accuracy, interfaces, or limitations. Regardless of their origin, they must be unambiguous and impose measurable requirements.

#### Specifications

The team's stakeholder for this project is Matt Manzaro, the general manager and club pro for the Golden Eagle Golf Course. Below are a list of specifications given from our stakeholder for this project.

The data metrics used in the display will include ball speed, clubhead speed, launch angle, spin rate, and smash factor. These are regarded to be the most important data metrics to display for both an amateur and experienced golfer.

The data metrics must be processed and used to make suggestions to the user on what to correct in their golf swing. It will also store the data allowing for the user to review it post training session or match.

The battery life of the headset needs to last a minimum of 9 holes, but ideally a full round of 18 holes. Based on the skill level of the golfer, the time frame that it will take to complete 9 holes can take anywhere from a minimum of 2 hours to a maximum of 3 hours[5]. For 18 holes, it would take anywhere from 4 to 6 hours to complete.

The system must also be portable allowing the user to have the option of using it for set training purposes or for match play. 

The launch monitor must include a feature where it is able to wirelessly communicate the data collected to either an app or software. The system will require the data collected to be communicated to it in order for it to work as described.

#### Constraints

The following are constraints that may limit the Smart Golf Visor depending on the chosen design approach. Any Bluetooth-based wireless transfer of information must comply with IEEE 802.15.1 standard for device networking and communication. The visor will require a power source to function, and if a lithium-ion battery is selected, it must adhere to IEC 62133 safety regulations for testing and usage. If a mini-LED projector is used for the visor’s display, it must comply with ISO 15004-2 eye safety standards for image projection near the eyes.

In addition to industry standards, the visor must also meet operational constraints to be effectively used by golfers. To ensure the golfer’s safety and allow for improved performance, the visor must not obstruct their vision as it could delay their reaction time and hinder their decision making. For the visor to be used outdoors, it must be designed to withstand various weather conditions. Sunlight can interfere with the display, reducing visibility, so the design will require technology capable of blocking or compensating for sunlight to maintain display clarity. To account for rain, the electronic components must be enclosed in waterproof casing to ensure protection against environmental exposure. 


## Survey of Existing Solutions

There are currently several launch monitors on the market that provide accurate measurements such as ball distance, spin rate, and club speed. These devices are widely used for training purposes and in golf simulators.
One example is the Garmin Approach R10, which delivers over a dozen swing and ball metrics while also recording video of the golfer’s swing[9]. Another more advanced and expensive option is the Full Swing KIT launch monitor, which provides 16 club and ball metrics with faster processing and patented radar technology[10]. These products demonstrate the range of features available, from consumer-friendly devices to professional-grade systems.
While these solutions excel at measuring swing and ball data, they rely on external screens such as phones, tablets, or computers to display results. Adding a linked real-time display through augmented reality (AR) glasses would create a fully immersive experience. With this approach, the user could view their performance data seamlessly during play, without interrupting their rhythm to check another device.
In summary, although the market offers many reliable options for swing and ball flight analysis, there does not appear to be a wearable interface that presents these metrics directly to the user. This gap creates an opportunity for a unique solution that combines accurate data tracking with an immersive, real-time display.



## Measures of Success

The success of this project will be evaluated based on two primary metrics:
1.	The ability of the glasses to accurately and reliably display swing and ball-flight data in real time.
2.	The overall practicality and ease of use of the glasses during a round of golf.
The first item of success is Data Display Functionality. For the glasses to be successful, they must reliably display swing and ball-flight metrics from the launch monitor in real time. This aspect will be measured by connecting the glasses to an existing launch monitor and verifying that all relevant metrics (distance, spin, club speed, etc.) are displayed correctly with minimal delay. The target for the first iteration is for the glasses to display metrics with a latency that does not interrupt their rhythm.
The second item of success lies within the glasses’ Practicality and Ease of Use during actual golfing. The glasses must be lightweight, comfortable, and unobtrusive, allowing the golfer to wear them throughout a round without distraction. The team will test this aspect by conducting trial sessions with golfers and collecting feedback on comfort, usability, and impact on pace of play. 
In summary, the project will be considered successful if the prototype glasses can both accurately display swing and ball metrics in real time and demonstrate practicality for golfers through positive user feedback.



## Resources


This section will go over the necessary resources of this project. It will start off with the estimated budget for this project. After the estimated budget is discussed, it will introduce the personel of the group and discuss each individual skills and how those skills can be used to help with this project. This section will then finish off with a timeline of the project where all major deadlines and small tasks will be included.

### Budget

The budget for the wearable smart golf visor is a tentative estimate and will be refined as requirements, vendor quotes, and test results are finalized. The budget will serve as an outline for the project and as a practical demonstration, indicating that the system can be built within a reasonable cost. The budget, shown in the figure, is organized by the subsystems that comprise the design to provide clarity on scope and expected cost for each area.

Subsystem categories include: launch monitor (primary sensing), visor display (AR optics and interface hardware), compute/bridge (smartphone or embedded processor and firmware), power and I/O (battery, PD hub, cabling, and connectors), mechanical integration (visor mount, enclosure, and fastening), software (HUD application, drivers, and tooling), and field accessories (stand, alignment aids, and protection). Low and high contingency allowances are included to account for price variability, component substitutions, and minor design changes; these bounds are used to gauge potential project cost.

After estimating the likely components within each subsystem, the suggested cost for designing and building the wearable golf visor HUD is summarized in the figure, with contingency applied as separate line items where appropriate. Assumptions include no mandatory subscriptions for baseline operation and one complete system per unit. Substitutions are permitted if alternatives meet performance and interface requirements.
![budget scenarios pic](https://hackmd.io/_uploads/r1qHXAe3ee.png)

### Personel

This team consists of six electrical engineering students who all share some similar skills but also possesses unique skills from one another. While some skills in this project are met by at least one team member, there are some skills gaps noticed within the team. One skill gap is image signal processing which will be used with tracking the ball from the camera system. For this project to be successful, the team will take the time to learn this as well as any other skills gap that are made apparent. 


Brandon Zerkovich was chosen to be the leader of this team and his responsibilities include delegating tasks amongst the team and making sure that deadlines will be met by each individual. Brandon's skills include C and C++ programming languages and power system analysis. These skills will help with the microcontroller and the powering system that will be used for this project.

Landen Johnson has experience in related course to c++, power systems, and mechatronics. He has past internship experiences relating to control systems in the utility industry, as well as communication systems. These internships also gave him experience in testing equipment to make sure it operates as it should. These skills will help with the communication aspects of the project and the later testing. However, he has no experience in PCB design and computer engineering coursework.

Ryan Zerkovich has internship experience working with control systems in a manufacturing setting. Throughout his time in college, he has learned and developed skills about power systems, microcontrollers, signal processing, circuit analysis and various programming languages. Some softwares he has worked with are CAD design and MATLAB.

Andrew Goostree has internship experience related to power systems, data processing and refining from the utility industry. He also has familiarity with microcontrollers, CAD Design, control systems, and some coding background. 

Bryce Hughes Has internship experience in power systems, and teting power systems. He has internship experience in AutoCad design, has experience in coding in c++ and c. 

Michael Madigan has internship and co-op experience related to process improvement, automation, and control systems. He also has familiarity with microcontrollers, CAD Design, 3D Printing and programming languages such as C, C++, MATLAB, and Python.

The supervisor that was chosen for this project is J.C. Williams. J.C. Williams has done work with radar technologies, digital and image signal processing, and computer vision. All of these subjects relate to what will be done in this project and also includes some subjects that the group has skill gaps in. This team values the knowledge and mentorship that he will provide during this project.

The instructor for this project is Dr. Storm Johnson. He will act essentially as the project manager where he will oversee and approve all documentation and reports that the team writes. He will also oversee the assembly and testing of the project. The team will have weekly meetings with Storm to discuss weekly progress, decisions made from the group, and any conflicts or problems the team experiences.

### Timeline

Our team's Gantt Chart is pictured below. We will utilize it as a living document to update progress on known tasks and add future tasks as they become available. Holidays, definitive due dates, and research topics are included for the Fall 2025 semester. Tentative timelines for the Spring 2026 semester are included in the chart and will be finalized as they are assigned.

Fall 2025 Semester:
![GanttChart_Sem1](https://hackmd.io/_uploads/S1YhCAZ3gl.png)

Spring 2026 Semester:
![GanttChart_Sem2](https://hackmd.io/_uploads/r1YhAC-nxl.png)


## Specific Implications

The development of the Smart Golf Visor has significant implications for enhancing training methodologies in golf by providing quantitative, real-time feedback directly to the user. Through the integration of launch monitor technology with real-time visualization, the system will enable immediate assessment of ball-flight parameters, including launch speed and trajectory. Readily presenting this information allows the user to make data-driven adjustments to swing mechanics during practice sessions and club selections during course play.

In addition to real-time visualization, the system’s proposed ability to archive shot data will establish a robust performance database. This feature will support analysis of user trends, such as distance, shot shape, consistency, club-specific performance, and changes in these data over time. By transforming practice events into structured datasets, the visor will facilitate evidence-based evaluation of skill development. The incorporation of these analytics will further enhance the ability to detect subtle performance patterns, ultimately enabling tailored feedback and personalized training regimens.

For amateur golfers seeking improvement, the visor will provide access to professional-level data that was previously unavailable outside of expensive facilities, enabling faster skill development. Intermediate and competitive golfers will gain the advantage of measurable trend analysis, allowing them to fine-tune their performance over time. Golf instructors and coaches will benefit from the ability to deliver data-driven lessons, using the visor’s stored datasets to monitor student progress and enhance instructional precision. In addition, the system offers unique value for golfers seeking casual enjoyment; the ability to track ball location in real-time will reduce the frustration of lost balls and keep play moving smoothly.

The implications of the Smart Golf Visor could extend beyond the individual golfer. The system represents a scalable model for integrating affordable, compact AR-based analytics into sports training, a field traditionally reliant on high-cost equipment in specialized facilities. By making advanced performance metrics more accessible, the Smart Golf Visor will bring professional-level feedback within reach of amateur golfers, while still offering advanced users a portable and convenient training solution.



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
  - **Job Secuirity:** The product is intended to do the job of a caddie and possibly golf intructors. If the product works as intended or better it could possibly lead to the potential loss of jobs.


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

[1] “Different types of launch monitor,” Foresight Sports Europe, https://foresightsports.eu/foresight-life/different-types-of-launch-monitor/ (accessed Sep. 21, 2025). 

[2] “Specialist LCD/OLED supplier,” What Is a Transparent Micro OLED Display and How Does It Work? - OLED/LCD Supplier, https://www.panoxdisplay.com/solution/transparent-micro-oled-display-how-it-works/ (accessed Sep. 21, 2025). 

[3] “OLED microdisplays: Learn more and find a supplier!,” OLED, https://www.oled-info.com/oled-microdisplays (accessed Sep. 21, 2025). 

[4] J. Shepard, “The difference between lithium ion and lithium polymer batteries,” Battery Power Tips, https://www.batterypowertips.com/difference-between-lithium-ion-lithium-polymer-batteries-faq/ (accessed Sep. 21, 2025). 

[5] Glenmuir, “How long to play 9 holes of golf,” Glenmuir Icon, https://www.glenmuir.com/news/9-holes-of-golf (accessed Sep. 23, 2025). 

[6] "Part 15.1: Wireless medium access control (MAC)
and physical layer (PHY) specifications for
wireless personal area networks (WPANs)", IEEE, https://ieeexplore-ieee-org.sdl.idm.oclc.org/stamp/stamp.jsp?tp=&arnumber=9034048 (accessed Sep. 24, 2025).

[7] “Ophthalmic instruments — Fundamental requirements and test methods — Part 2: Light hazard protection,” ISO, https://www.iso.org/obp/ui/en/#iso:std:iso:15004:-2:ed-2:v1:en (accessed Sep. 24, 2025).

[8] "IEC 62133: Safety Testing for Lithium Ion Batteries", IEC, https://webstore.iec.ch/en/iec_catalog/product/preview/?id=L3B1Yi9wZGYvcHJldmlldy9pbmZvX2llYzYyMTMzLTJ7ZWQxLjB9Yi5wZGY= (accessed Sep. 24, 2025)

[9] Garmin. Garmin Approach® R10 | Portable Golf Launch Monitor [Online]. Available: https://www.garmin.com/en-US/p/695391/

[10] Full Swing. KIT for Back 9 – Full Swing Golf Simulators | Champion Proven Technology [Online]. Available: https://www.fullswinggolf.com/kit-back9/

[11] IEEE, “IEEE code of ethics | IEEE,” IEEE Advancing Technology for Humanity, https://www.ieee.org/about/corporate/governance/p7-8

[12] Samainstage, “IEEE launches new standard to address ethical concerns during systems design,” IEEE Standards Association, https://standards.ieee.org/news/ieee-7000/ (accessed Sep. 24, 2025). 
 
[13] “Best Launch Monitor Under $1,000: Rapsodo vs. Garmin vs. Swing Caddie,” Breaking Eighty, https://breakingeighty.com/best-launch-monitor-under-1000 (accessed Sep. 20, 2025).


## Statement of Contributions

Each team member must contribute meaningfully to the project proposal. In this section, each team member is required to document their individual contributions to the report. One team member may not record another member's contributions on their behalf. By submitting, the team certifies that each member's statement of contributions is accurate.

Brandon Zerkovich worked on gathering and writing the background information under the formulating the problem section of the report. He also helped with writing the specifications and the personal sections of the report as well.

Landen Johnson worked on formulating the introduction and ethics, responsibilities, and implications subsections of the report. He also added to his own personel section above.

Ryan Zerkovich worked on researching standards and constraints that the design approach will need to abide by, as well as updating his section in the personal background.

Andrew Goostree worked on creating the budget and the items listed in the Resources section, gathering information needed to accurately compile a budget. He also listed relevant experience and skills for this project. 

Bryce Hughes complteted survey of existing solutions, as well as creating the measurement of sucess section. He also listed his relevent experience and skills for this project. 

Michael Madigan completed the Timeline and Specific Implications sections of this document, as well as his personal background section. He also developed the first draft of the Gantt Chart to be updated throughout the project.
