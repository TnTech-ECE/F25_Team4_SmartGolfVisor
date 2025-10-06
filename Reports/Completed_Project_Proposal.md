# Smart Golf Visor Project Proposal

## Introduction

Golfers often make decisions on the course with incomplete information: they may not know exactly how far their shots carried, at what speed the ball left the club, spin rate of the ball, precisely where the ball landed, or the efficiency the ball is being struck at. The Smart Golf Visor is a wearable, real-time feedback system designed to deliver key shot metrics (ball speed, ball distance, launch angle, spin rate, and smash factor.) along with a visual indication of shot trajectory. The system incorporates an affordable launch monitor, see through visor dislay, and a rechargable battery powering system.  

**Inclusive / Accessibility Extension:** For users who have low vision, the Smart Golf Visor offers high-contrast large text. This allows these users to independently track where the ball went, how far, and makes the game more accessible to a wider audience.

### Proposal Outline

This proposal will cover the following:

1. **Background & Motivation** — Prior work in golf analytics, shot feedback systems, and assistive sports technology.  
2. **Design & Technical Approach** — Hardware components, software architecture, display and feedback modes, and power management.  
3. **Implementation Plan** — Timeline, prototypes, testing, and team roles.  
4. **Evaluation** — Measurements of success: accuracy of shot metrics, latency of feedback, battery life, and reliability in different conditions.  
5. **Budget & Resources** — Detailed cost breakdown, existing resources, and possible trade-offs in cost, weight, accuracy, and size.  
6. **Broader Implications, Ethics, & Responsibility** — Global, economic, environmental, societal impacts; ethical issues; mitigation strategies; responsibilities of engineering team.

## Formulating the Problem
This section of the project proposal covers the background information, specifications given to the team by the customer and will conclude by discussing the constraints for this project. Both the specifications and constraints will help with narrowing down the design parameters.
### Background

The objective of the Smart Golf Visor is to create a system capable of processing and displaying information from a launch monitor and an additional camera system in real-time. The possible solutions for the visor are augmented reality glasses, an over-the-head headset, or a see-through wearable eye visor. The system will be hands-free with the only exception being setting down and picking up the launch monitor. Below is a more detailed background explanation of the components that will be used in this system.

#### *Launch Monitor*
A launch monitor is a device that uses camera-based or radar-based tracking technology to measure crucial data points of a golf swing like ball speed, ball distance, launch angle, etc. 

Launch monitors that use camera-based tracking technology include high-speed cameras that capture images of the club head and ball at impact. The number of images captured ranges from 2 and 200+ [1]. From the captured images, the data of the launch conditions is compared to a ball-flight model to determine the distance and flight of the ball. The advantage this camera-based tracking system provides is that it takes direct measurements of the launch conditions without being affected by conditions that impact ball flight. The accuracy of camera-based launch monitor comes down to the quantity and quality of the pictures taken. Below is a picture of what a monitor using a camera-based system looks like.
<p align="center">
  <img width="400" height="400" src="https://hackmd.io/_uploads/H1wrtkAslx.png">
</p> 

 <p align="center"> Figure 1: Camera-based tracking Launch Monitor </p>
 

Radar-based launch monitors use doppler radar to emit radio waves that reflect off the ball and club after impact and are recieved at the monitor which allows for the flight of the ball to be determined after impact. Flight data of the golf ball is gathered and put into a complex algorithm to then calculate and measure the launch conditions that caused that flight [1]. The advantage of the radar-based tracking system is that it is able to show where and how far the ball has traveled. The accuracy of this monitor comes down to quality of the radar as well as the correctness of the algorithm. Below is a picture of a monitor that uses a radar-based system.

<p align="center">
  <img width="400" height="400" src="https://hackmd.io/_uploads/SkcY4yRiex.png">
</p> 

 <p align="center"> Figure 2: Radar-based tracking Launch Monitor </p>

Camera-based and radar-based tracking launch monitors are the best solution to gather the most accurate ball speed, ball distance, launch angle, spin rate, and smash factor from a golf shot. 

#### *Display*
The display will be a projection onto a lens that will show the ball speed, ball distance, launch angle, spin rate, and smash factor gathered from the launch monitor.  The display will also show whether the ball went left, right or straight of the fairway. Future improvements include a ribbon tracing of the shot. The display will come from a transparent, micro-OLED display which is common with near-eye display technology. A transparent OLED display works by generating light pixels on a clear subtrate like glass. The self-emissive microscopic pixels are spaced to permit see-through visibility, balancing image brightness and transparency for a seamless augmented experience [2]. Key advantages include fast refresh rates, low power consumption, and the absence of backlight [3]. Figure[3] shows a picture of a micro-OLED display.

<p align="center">
  <img width="400" height="400" src="https://hackmd.io/_uploads/rJqsxZRjex.png">
</p> 

 <p align="center"> Figure 3: Micro-OLED Display </p>
 
The display is hardwired to the microcontroller, guaranteeing reliable signal transmission with minimal latency and ensuring precise, distortion-free projection. 

#### *Powering System*
The powering system of the visor will involve a battery that will be used to power all components which will include the microcontroller and display but will not include the launch monitor. Rechargable batteries can be used for this system. Lithium-ion polymer (LiPo) batteries and regular lithium-ion batteries are both rechargable. LiPos batteries contain a gel-like polymer electrolyte packaged in an aluminum foil pouch allowing for a more flexible design than a regular lithium-ion battery that contains a liquid electrolyte in a stainless steel or alimunum case. LiPos also provide higher specific energies than other lithium batteries and are often used in systems where weight is an important factor [4]. However LiPos do have a shorter lasting lifespan than regular Lithium-ion batteries. LiPo batteries are the more design ready and widely accessible battery than regular Lithium-ion batteries. Figure[4] shows a LiPo battery and figure[5] shows a regular lithium ion battery.

<p align="center">
  <img width="400" height="400" src="https://hackmd.io/_uploads/S1bBZl0oex.png">
</p> 

 <p align="center"> Figure 4: LiPo Battery </p>

<p align="center">
  <img width="400" height="400" src="https://hackmd.io/_uploads/BJo5Wp23eg.jpg">
</p> 

 <p align="center"> Figure 5: Regular Lithium-ion battery (Samsung 18650) </p>


Rechargable lithium-ion batteries perform superbly but can be rather unforgiving if operated outside a generally  safe operating area, with outcomes ranging from compromising the battery performance to outright dangerous consequences [5]. A battery management system (BMS) is a technology that provides a necessary oversight of the battery pack. The BMS constantly monitors the battery and provides both eletrical protection and thermal protection. Figure[6] shows a battery management system. 

<p align="center">
  <img width="400" height="400" src="https://hackmd.io/_uploads/HJL3p62nxx.jpg">
</p> 

 <p align="center"> Figure 6: Battery Management System </p>

The use of rechargable batteries will also require a specialized charging circuit so that constant current and voltage will be supplied and ensure a safe charge. The use of non-rechargable batteries is also an option and would eliminate the requirement for a specialized charging circuit. This would require the individual to have to carry spare batteries for the event of a dead battery.

### Specifications and Constraints

#### Specifications

The team's stakeholder for this project is Matt Manzaro, the general manager and club pro for the Golden Eagle Golf Course. Below are a list of specifications given from our stakeholder for this project.

1. The data metrics used in the display shall include ball speed, ball distance, launch angle, spin rate, and smash factor. These are regarded to be the most important data metrics to display for both an amateur and experienced golfer.
2. The data metrics shall be processed and used to make suggestions to the user on what to correct in their golf swing. It shall also store the data allowing for the user to review it post training session or match.
3. The battery life of the headset shall last a minimum of 9 holes, but ideally a full round of 18 holes. Based on the skill level of the golfer, the time frame that it will take to complete 9 holes can take anywhere from a minimum of 2 hours to a maximum of 3 hours [6]. For 18 holes, it would take anywhere from 4 to 6 hours to complete.
4. The system shall also be portable allowing the user to have the option of using it for set training purposes or for match play. 
5. The launch monitor shall have the ability to wirelessly communicate the data collected to either an app or software.

#### Constraints

The following are constraints the Smart Golf Visor shall abide by:
1) Any Bluetooth-based wireless transfer of information shall comply with IEEE 802.15.1 standard that defines how Personal Area Network (PAN) devices wirelessly connect and transfer management information within a 10 meter radius. 
2) The use of a Lithium Ion or Lithium Polymer battery as a power source shall comply with IEC 62133 safety regulations to ensure safe battery operation within extreme temperatures, electrical faults, and mechanical stresses.  
3) Any image projection onto the glass's lense shall comply with ISO 15004-2 eye safety standards that determines the group hazard level of optical radiation within wavelength range of 250 nm to 2500 nm.
4) The electronic components within the visor shall be IP-67 Rated providing dirt protection for objects greater than 1mm diameter and water protection for depths of 15 centimeters to 1 meter.  

In addition to industry standards:
1) The view shall not obstruct the view of the golfer to ensure safety and improved performance.
2) The display shall be capable of maintaining a clear and readable display. 


## Survey of Existing Solutions

#### Launch Monitors
There are several launch monitors commercially available that provide accurate measurements for ball speed, ball distance, launch angle, spin rate, and smash factor. These devices are widely used for training purposes and in golf simulators.

One example is the Garmin Approach R10, which is known for its high peformance-to-price ratio. It is designed to deliver over a dozen swing and ball metrics while also recording video of the golfer’s swing [10]. The benefits provided are its compact and lightweight design, strong battery life where it can last up to 10 hours on a single charge, and more accessible cost compared to high-end launch monitors. The notable drawbacks of this monitor is its limited tracking for putting and short game and its issue with delivering accurate spin rate. 

Another more advanced and expensive example is the Full Swing KIT launch monitor, which provides 16 club and ball metrics with faster processing and patented radar technology [11]. The benefits of using a more advanced monitor are the ability to gather more comprehensive data metrics like horizontal launch angles and apex height, uses built in video to capture swing, and its ability for short game tracking. A major drawback though is the expensive cost and the requirement of a subscription to access its complete capabilities. These products demonstrate the range of features available, from consumer-friendly devices to professional-grade systems. There are many commercially availiable launch monitors that are accuarate however the data display metrics can impact pace of play and the rhythm of the golfer by causing them to stop and study the data on a phone or small built in display.

#### AI Swing Coach
AI swing coach softwares use artificial intelligence and computer vision to analyze a golfer's swing and provide them feedback. The software captures the swing data on video by a camera usually on a smartphone or high-end launch monitor. The benefits of using this is the ability to gain personalized feedback without having to spend money on private lessons, convience of using it anytime and anywhere without needing a coach present, and improve both beginning and advanced players' game. The drawbacks of using this is the limited context the software is able to comprehend of a player's physical limitations and the requirement of additional subscriptions. These programs also require time for processing and the use of a phone to view the analyzation between each shot. Using them on the course is not practical due to the decrease pace of play it it will lead to during a round.

#### Smart Glasses
There are a variety of smart glasses commercially availiable with the Rokid Max and Xreal Air 2 Pro being two examples. Both of these products have many capibilities and are used as a daily life enhancer. 

The Rokid Max smart glasses display at a 120 Hz refresh rate and a 600 nits percieved brightness. It also allows for easy conectivity with devices with USB-C DisplayPort and OTG. The XREAL Air Pro 2 has a 120 Hz display as well and a 500 nits percieved brightness, and connects via USB-C video output. Both these glasses have a weight of 75 grams and many added comfort features. The glasses both also have easy access to interfacing for custom HUD displays.


The drawbacks for both of these glasses are the same as they are used as a daily entertainment and not configured for assisting golfers. A notable drawback for the XREAL Air Pro 2 glasses is that it only offer a 47 degree field-of-view which is considered relatively narrow compared to other smart glasses. A notable drawback the Rokid Max glasses has is its including no support for farsighted individuals.


#### Insights and Gaps

While launch monitors excel at data accuracy and AI apps offer advanced feedback, both depend on external screens such as phones or tablets. Smart glasses introduce hands-free visualization but lack sport-specific applications. This creates a clear gap in the market for a hybrid solution that combines precise launch data collection with a wearable, real-time AR display.

#### Takeaways

1. Adopt radar or vision-based data collection methods from current launch monitors.
2. Apply the usability concepts from existing smart glasses for hands-free feedback.
3. Address the major gap: an integrated system allowing golfers to view real-time swing metrics without disrupting play.


In summary, although the market offers many reliable options for swing and ball flight analysis, there is not a wearable interface that presents these metrics directly to the user. This gap creates an opportunity for a unique solution that combines accurate data tracking with an immersive, real-time display.



## Measures of Success

The success of this project will be evaluated based on three primary metrics:
1.	The ability of the glasses to accurately and reliably display swing and ball-flight data in real time.
2.	The overall practicality and ease of use of the glasses during a round of golf.
3.	The meeting of industry and team standards.

The first item of success is Data Display Functionality. For the glasses to be successful, they must reliably display swing and ball-flight metrics from the launch monitor in real time. This aspect will be measured by connecting the glasses to an existing launch monitor and verifying that all relevant metrics (ball speed, ball distance, launch angle, spin rate, and smash factor) are displayed correctly with minimal delay. The target for the first iteration is for the glasses to display metrics with a latency between 3 to 5 seconds. While maintaining consistant for 10 trials. This data will be verified by the golfer and the data displayed on the launch monitor. Measure number one will satisify these specifications. The data metrics used in the display shall include ball speed, ball distance, launch angle, spin rate, and smash factor. These are regarded to be the most important data metrics to display for both an amateur and experienced golfer.

The second item of success lies within the glasses’ practicality and ease of use during golfing. The glasses must be lightweight, comfortable, and unobtrusive, allowing the golfer to wear them throughout a round without distraction. The team will test this aspect by conducting trial sessions with golfers and collecting feedback on comfort, usability, and impact on pace of play using a  1 to 5 scale 5 being a perfect product. Measure two will satisfy this specifications, The battery life of the headset shall last a minimum of 9 holes, but ideally a full round of 18 holes. Based on the skill level of the golfer, the time frame that it will take to complete 9 holes can take anywhere from a minimum of 2 hours to a maximum of 3 hours [6]. For 18 holes, it would take anywhere from 4 to 6 hours to complete, and the system shall also be portable allowing the user to have the option of using it for set training purposes or for match play. 

The third measure of success will be by meeting the following four industry constraints as well as two additional constraints set by our team
1. Any Bluetooth-based wireless transfer of information shall comply with IEEE 802.15.1 standard that defines how Personal Area Network (PAN) devices wirelessly connect and transfer management information within a 10 meter radius.
2. The use of a Lithium Ion or Lithium Polymer battery as a power source shall comply with IEC 62133 safety regulations to ensure safe battery operation within extreme temperatures, electrical faults, and mechanical stresses.
3. Any image projection onto the glass's lense shall comply with ISO 15004-2. eye safety standards that determines the group hazard level of optical radiation within wavelength range of 250 nm to 2500 nm.
4. The electronic components within the visor shall be IP-67 Rated providing dirt protection for objects greater than 1mm diameter and water protection for depths of 15 centimeters to 1 meter.
In addition to industry standards the team standards are:
5. The display shall not obstruct the view of the golfer to ensure safety and improved performance.
6. The display shall be capable of maintaining a clear and readable display.

These requirements will be tested by our team in a lab setting to verify.

In summary, the project will be considered successful if the prototype glasses can both accurately display swing and ball metrics in real time and demonstrate practicality for golfers through positive user feedback throughout the life of the product, and finally if all specifications and standards are met.



## Resources


This section will go over the necessary resources of this project that include the estimated budget, the personel of the Smart Golf Visor team and a timeline that lays out all major and minor deadlines during the duration of this project.

### Budget

The budget for the wearable smart golf visor is a tentative estimate and will be refined as requirements, vendor quotes, and test results are finalized. The budget will serve as an outline for the project and as a practical demonstration, indicating that the system can be built within a reasonable cost. The budget, shown in Figure[7], is organized by the subsystems that comprise the design to provide clarity on scope and expected cost for each area.

Main subsystem categories include: launch monitor (primary sensing), visor display (wearable AR eyewear serving as a USB-C/HDMI display sink), and powering the system (I/O, battery, PD hub, BMS, cabling, and connectors). Low and high contingency allowances are included to account for price variability, component substitutions, and minor design changes; these bounds are used to gauge potential project cost. 

The launch monitor is the core instrument producing shot kinematics. Its portable setup, suitable for the driving range, practice net, and on-course use, allows the user to see real-time metrics. Spending half of the budget on the launch monitor will ensure the accuracy of the shot projection and other metrics for the user. The visor display is head-worn AR eyewear acting as a USB-C/HDMI wired link. This provides a bounded, repeatable video path with frame-accurate updates, optics and placement are specified for daylight legibility, minimal occlusion, and safe cable routing. Spending approximately a third of the budget on the AR glasses will ensure a high quality display to read the metrics given from the launch monitor. Power/ I/O provides stable USB-C short cables and strain relief preserve voltage margin and prevent disconnects during swing, and the battery’s BMS supplies over-current and thermal protection. Small integration consumables (connectors, adapters, cable clips) are included within power/ I/O. The price for the power system allows the system to run for the entire round of golf.

After estimating the likely components within each subsystem, the suggested cost for designing and building the wearable golf visor is summarized in  Figure[7], with contingency applied as separate line items where appropriate. Assumptions include no mandatory subscriptions for baseline operation and one complete system per unit. 

<p align="center">
  <img width="800" height="800" src="https://hackmd.io/_uploads/H1lkp2a3ee.png">
</p> 

 <p align="center"> Figure 7: Smart Golf Visor Budget </p>

 
### Personel

The Smart Golf Visor team is composed of six electrical engineering students who share similar academic coursework while bringing diverse skills through their individual internship experiences. Although many project requirements are met by at least one team member, certain skill gaps have been identified. In particular, image signal processing which is a key componenet used with tracking the ball from the camera system has been identified as an area requiring futher development. To ensure the project's success, the team is committed to learning this as well as any other skills gap that are made apparent. 


Brandon Zerkovich was chosen to be the leader of this team and his responsibilities include delegating tasks amongst the team and making sure that deadlines will be met by each individual. Brandon's skills include C and C++ programming languages and power system analysis. These skills will help with the microcontroller and the powering system that will be used for this project.

Landen Johnson has experience in related courses such as control system analysis, power system analysis, introductory power systems, data structures and algorithms, and mechatronics. He has past internship experiences relating to control systems in the utility industry, as well as communication systems. These internships provided him with experience in testing and evaluating equipment to ensure its proper operation. These skills will facilitate the communication aspects of the project and subsequent testing phases. However, a lack of experience in PCB design and computer engineering coursework will be addressed through targeted training.

Ryan Zerkovich has internship experience related to automation including process improvements and data processing. He has developed skills from prior coursework in power systems, microcontrollers, signal processing, circuit analysis and C++ programming language. These skills will help with data communication and the power system.  

Andrew Goostree has internship experience related to power systems, data processing and refining from the utility industry. He also has familiarity with microcontrollers, CAD Design, control systems, and coding experience in C, C++, and Matlab. 

Bryce Hughes has internship experience in testing and evaluating power systems, along with a strong familiarity in AutoCAD design. He is also proficient in C and C++ programming. 

Michael Madigan has internship and co-op experience related to process improvement, automation, and control systems. He also has familiarity with microcontrollers, CAD Design, 3D Printing and programming languages such as C, C++, MATLAB, and Python.

The supervisor that was chosen for this project is J.C. Williams. J.C. Williams has done work with radar technologies, digital and image signal processing, and computer vision. These subjects are directly relevant to this project and will provide valuable guidance. This team values the knowledge and mentorship that he will provide during this project.

The instructor for this project is Dr. Storm Johnson where he will act as the project manager by overseeing and approving all documentation from the team as well as the assembly and testing of the project. The team will have weekly meetings with Storm to discuss progress, group decisions, and any team conflicts.

### Timeline

The team's Gantt Chart is pictured below. The team will utilize it as a living document to update progress on known tasks and add future tasks as they become available. Holidays, definitive due dates, and research topics are included for the Fall 2025 semester (Figure[8]). Tentative timelines for the Spring 2026 semester (Figure [9]) are included in the chart and will be finalized as they are assigned.

<p align="center">
  <img width="400" height="400" src="https://hackmd.io/_uploads/S1YhCAZ3gl.png">
</p> 

 <p align="center"> Figure 8: Gantt Chart showing the Fall 2025 Semester </p>

<p align="center">
  <img width="400" height="400" src="https://hackmd.io/_uploads/r1YhAC-nxl.png">
</p> 

 <p align="center"> Figure 9: Gantt Chart showing the Spring 2026 Semester </p>


## Specific Implications

The development of the Smart Golf Visor has significant implications for enhancing training methodologies in golf by providing quantitative, real-time feedback directly to the user. Through the integration of launch monitor technology with real-time visualization, the system will enable immediate assessment of ball-flight parameters, including launch speed and trajectory. Readily presenting this information allows the user to make data-driven adjustments to swing mechanics during practice sessions and club selections during course play.

In addition to real-time visualization, the system’s proposed ability to archive shot data will establish a robust performance database. This feature will support analysis of user trends, such as distance, shot shape, consistency, club-specific performance, and changes in these data over time. By transforming practice events into structured datasets, the visor will facilitate evidence-based evaluation of skill development. The incorporation of these analytics will enhance the ability to detect subtle performance patterns, ultimately enabling tailored feedback and personalized training regimens.

For amateur golfers seeking improvement, the visor will provide access to professional-level data that was previously unavailable outside of expensive facilities, enabling faster skill development. Intermediate and competitive golfers will gain the advantage of measurable trend analysis, allowing them to fine-tune their performance over time. Golf instructors and coaches will benefit from the ability to deliver data-driven lessons, using the visor’s stored datasets to monitor student progress and enhance instructional precision. In addition, the system offers unique value for golfers seeking casual enjoyment; the ability to track ball location in real-time will reduce the frustration of lost balls and keep play moving smoothly.

The implications of the Smart Golf Visor could extend beyond the individual golfer. The system represents a scalable model for integrating affordable, compact AR-based analytics into sports training, a field traditionally reliant on high-cost equipment in specialized facilities. By making advanced performance metrics more accessible, the Smart Golf Visor will bring professional-level feedback within reach of amateur golfers, while still offering advanced users a portable and convenient training solution.



## Broader Implications, Ethics, and Responsibility as Engineers

[//]: # "Consider the project’s broader impacts in global, economic, environmental, and societal contexts. Identify potential negative impacts and propose mitigation strategies. Detail the ethical considerations and responsibilities each team member bears as an engineer."
### Global, Economic, Environmental, Societal Impacts

- **Global / Economic Impacts:**  
  - Potential to create an inclusive sports tool usable worldwide. Could be used as a cheaper training tool for those without large incomes.  
  - Could lead to small business or startup opportunities in sports/assistive tech.  
  - Text can be produced in any language and all metrics can be converted into regionally appropriate formats.


- **Environmental Impacts:**  
  - **Electronic waste concerns:** batteries, displays, sensors may degrade or become obsolete overtime.  
  - **Energy consumption:** devices that draw high power degrade batteries faster and will need more frequent charging.  
  - **Materials:** sourcing, manufacturing of electronics has environmental footprint. Choosing durable, repairable, and recyclable components reduces impact.

- **Societal Impacts:**  
  - **Accessibility & Inclusion:** Makes golf more accessible to low vision individuals, helping them enjoy the sport more fully and independently.  
  - **Education:** Opportunity for STEM learning; engineering students gain experience building inclusive technology. Introduces golfers to the engineering/STEM process.  
  - **Job Secuirity:** The product is intended to do the job of a caddie and possibly golf intructors. If the product works as intended or better it could possibly lead to the potential loss of jobs.


### Ethical Considerations and Mitigation Strategies
<center>Table I
    
Ethical coniderations and potienial impacts</center>


| **Ethical / Responsible-Engineering Issue** | **Potential Negative Impact** | **Mitigation Strategy** |
|---|---|---|
| **Privacy & Data Consent** | Recording video may inadvertently capture bystanders; user may not want location / performance data stored or shared. | Design system so data remains local; encrypt stored data.|
| **Bias, Reliability, Fairness** | Computer vision or sensors may perform poorly in low light, glare, or certain clothing; can mis-locate shot or mis-estimate distance. | Extensive testing in different settings; build error margins / confidence levels; user feedback loop. |
| **Accessibility & Usability** | If the device is cumbersome, heavy, or non-intuitive,small displays hard to read. | Large/high-contrast text; ergonomic design; make controls simple; ensure display blocks a minimal field of view. |
| **Safety & Distraction** | Heads-up displays or wearable screens could distract users | Limit visual overlays; allow user to pause/disable feedback. |
| **Environmental Footprint** | Frequent replacement of batteries / components; disposables; poor lifecycle planning. | Use rechargeable batteries; design modular systems so parts can be replaced; pick durable hardware. |

### Responsibilities of Engineering Team

- Be transparent about capabilities and limitations.  
- Respect user privacy and autonomy in data collection, storage, sharing.  
- Strive for environmental sustainability: choose responsible hardware, consider lifespan and repairability.  
- Maintain safety, usability, and ethical integrity.
---


## References

[//]: # "All sources used in the project proposal that are not common knowledge must be cited. Multiple references are required."

[1] “Different types of launch monitor,” Foresight Sports Europe, https://foresightsports.eu/foresight-life/different-types-of-launch-monitor/ (accessed Sep. 21, 2025). 

[2] “Specialist LCD/OLED supplier,” What Is a Transparent Micro OLED Display and How Does It Work? - OLED/LCD Supplier, https://www.panoxdisplay.com/solution/transparent-micro-oled-display-how-it-works/ (accessed Sep. 21, 2025). 

[3] “OLED microdisplays: Learn more and find a supplier!,” OLED, https://www.oled-info.com/oled-microdisplays (accessed Sep. 21, 2025). 

[4] J. Shepard, “The difference between lithium ion and lithium polymer batteries,” Battery Power Tips, https://www.batterypowertips.com/difference-between-lithium-ion-lithium-polymer-batteries-faq/ (accessed Sep. 21, 2025). 

[5] “What is a Battery Management System (BMS)? – how it works,” Synopsys, https://www.synopsys.com/glossary/what-is-a-battery-management-system.html (accessed Oct. 2, 2025). 

[6] Glenmuir, “How long to play 9 holes of golf,” Glenmuir Icon, https://www.glenmuir.com/news/9-holes-of-golf (accessed Sep. 23, 2025). 

[7] "Part 15.1: Wireless medium access control (MAC)
and physical layer (PHY) specifications for
wireless personal area networks (WPANs)", IEEE, https://ieeexplore-ieee-org.sdl.idm.oclc.org/stamp/stamp.jsp?tp=&arnumber=9034048 (accessed Sep. 24, 2025).

[8] “Ophthalmic instruments — Fundamental requirements and test methods — Part 2: Light hazard protection,” ISO, https://www.iso.org/obp/ui/en/#iso:std:iso:15004:-2:ed-2:v1:en (accessed Sep. 24, 2025).

[9] "IEC 62133: Safety Testing for Lithium Ion Batteries", IEC, https://webstore.iec.ch/en/iec_catalog/product/preview/?id=L3B1Yi9wZGYvcHJldmlldy9pbmZvX2llYzYyMTMzLTJ7ZWQxLjB9Yi5wZGY= (accessed Sep. 24, 2025)

[10] Garmin. Garmin Approach® R10 | Portable Golf Launch Monitor [Online]. Available: https://www.garmin.com/en-US/p/695391/

[11] Full Swing. KIT for Back 9 – Full Swing Golf Simulators | Champion Proven Technology [Online]. Available: https://www.fullswinggolf.com/kit-back9/

[12] IEEE, “IEEE code of ethics | IEEE,” IEEE Advancing Technology for Humanity, https://www.ieee.org/about/corporate/governance/p7-8

[13] Samainstage, “IEEE launches new standard to address ethical concerns during systems design,” IEEE Standards Association, https://standards.ieee.org/news/ieee-7000/ (accessed Sep. 24, 2025). 
 
[14] “Best Launch Monitor Under $1,000: Rapsodo vs. Garmin vs. Swing Caddie,” Breaking Eighty, https://breakingeighty.com/best-launch-monitor-under-1000 (accessed Sep. 20, 2025).


## Statement of Contributions


Brandon Zerkovich worked on gathering and writing the background information under the formulating the problem section of the report. He also helped with writing the specifications and the personal sections of the report as well.

Landen Johnson worked on formulating the introduction and ethics, responsibilities, and implications subsections of the report. He also added to his own personel section above.

Ryan Zerkovich worked on researching standards and constraints that the design approach will need to abide by, as well as updating his section in the personal background.

Andrew Goostree worked on creating the budget and the items listed in the Resources section, gathering information needed to accurately compile a budget. He also listed relevant experience and skills for this project. 

Bryce Hughes complteted survey of existing solutions, as well as creating the measurement of sucess section. He also listed his relevent experience and skills for this project. 

Michael Madigan completed the Timeline and Specific Implications sections of this document, as well as his personal background section. He also developed the first draft of the Gantt Chart to be updated throughout the project.
