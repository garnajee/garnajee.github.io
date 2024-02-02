---
title: "Exploring Windows Driver Vulnerabilities: The Reverse Engineering Approach"
date: 2024-02-02T14:20:56+010
summary: "Unraveling Windows driver security through reverse engineering"
aliases: ["/posts/reverse-drivers","/posts/windows-driver-security","/posts/driver-vulnerability-analysis"]
tags: ["reverse", "reverse engineering", "windows", "drivers", "cybersecurity", "vulnerability", "kernel"]
author: "Garnajee"
ShowWordCount: true
draft: true
---

**Abstract:**

This project, conducted as part of the final year of an engineering degree in Cyberdefense at ENSIBS, delves into the intricate realm of Windows driver security. It's focused on their critical analysis and explores strategies for their assessment and mitigation. Through rigorous research and practical exploration, the project aims to contribute to the advancement of cybersecurity practices, equipping future professionals with essential insights and methodologies to safeguard against evolving threats within the Windows operating environment.

# Introduction
In the intricate landscape of operating systems, Windows drivers serve as crucial components, facilitating seamless communication between hardware and software. These drivers bridge the gap between user applications and hardware peripherals, enabling essential functions such as graphics rendering, network communication, and device management. Without drivers, the interaction between the operating system and hardware components would be impossible, underscoring their pivotal role in system functionality and performance.

Reverse engineering emerges as a pivotal technique in scrutinizing the intricate workings of Windows drivers. By dissecting operational software components, reverse engineering endeavors to unravel the technical intricacies and functional specifications embedded within drivers. This process facilitates a deeper understanding of driver behavior, enabling researchers and developers to identify vulnerabilities, deprecated code, and potential security loopholes.

Despite their indispensable role, Windows drivers harbor inherent vulnerabilities that can compromise system integrity and expose users to security risks. Malicious actors often exploit these vulnerabilities to gain unauthorized access, escalate privileges, or execute arbitrary code within the system. Securing Windows drivers assumes paramount importance in fortifying system resilience against evolving cyber threats and safeguarding user data and privacy.

# 1. Understanding the Windows Driver Security

## 1.1. Kernel-level drivers: WDM vs. WDF
Within the Windows operating system, drivers operate at varying privilege levels, with kernel-level drivers holding the highest level of access and responsibility. Two primary frameworks for developing kernel-level drivers in Windows are the Windows Driver Model (WDM) and the Windows Driver Framework (WDF).

* Windows Driver Model (WDM):

WDM drivers are a traditional approach to developing drivers for Windows operating systems.
They interact closely with the operating system kernel and directly access hardware resources.
Due to their direct interaction with system components, WDM drivers require precise memory management and error handling to prevent system crashes and vulnerabilities.
The development of WDM drivers necessitates a deep understanding of system architecture and kernel programming, making them prone to errors and vulnerabilities if not implemented correctly.

In the architecture of WDM drivers, the `DriverEntry` function serves as the primary initialization routine, responsible for setting up the driver environment and initializing driver-specific data structures. WDM drivers interact with the operating system kernel through dispatch routines linked to various IRP (I/O Request Packet) codes, enabling communication and interaction with hardware devices.

WDM drivers rely on pointers to system objects and structures, often referenced directly within the driver code. The model is implicit, with roles (physical, functional, or filter) determined implicitly by the driver's behavior. Context management in WDM drivers involves configuring context zones within driver-specific data structures, where synchronization and concurrency control require manual implementation using locks and synchronization primitives.

* Windows Driver Framework (WDF):

Introduced as a more structured and secure framework for driver development, WDF offers higher-level abstractions and simplifies driver development.
WDF drivers interact with the system through framework-provided APIs, reducing the likelihood of direct errors in memory management and resource allocation.
By abstracting complex interactions with the system, WDF drivers promote safer and more reliable driver development practices.
WDF drivers benefit from built-in error handling and resource management capabilities, minimizing the risk of common driver-related vulnerabilities.

In contrast to WDM, WDF incorporates the driver's entry point within the framework itself, simplifying initialization through event callback functions such as `EvtDriverDeviceAdd` for Plug and Play scenarios. Dispatch routines in WDF drivers are managed by the framework, reducing the burden on developers and ensuring consistent behavior across different driver implementations.

The object-oriented model in WDF introduces an opaque structure, where drivers interact with objects through handles provided by the framework. Object roles (physical, functional, or filter) are explicit and defined through event callback functions corresponding to each role. WDF also offers built-in support for context management, with configurable context zones for storing driver-specific data associated with each object instance. Synchronization is integrated into the framework, reducing the need for explicit locking mechanisms and minimizing the risk of synchronization-related issues.


### 1.2. Exploiting Vulnerable Drivers: The BYOVD Attack

#### 1.2.1. Understanding the BYOVD Threat

The Bring Your Own Vulnerable Driver (BYOVD) attack represents a sophisticated method of exploiting vulnerable drivers within the Windows operating system. In this attack, perpetrators surreptitiously introduce legitimate, yet compromised, drivers onto target systems. Once deployed, these drivers operate at the highest privilege level, known as ring 0, within the system's kernel. What makes BYOVD particularly insidious is its ability to evade traditional security measures. Legitimate drivers, even when compromised, are not inherently flagged by conventional security solutions, allowing attackers to operate undetected.

#### 1.2.2. The Significance of BYOVD Vulnerabilities

The gravest danger posed by BYOVD lies in its exploitation of signed certificates. Historically, signed certificates have served as a hallmark of trust and security within the Windows ecosystem. However, recent attacks have underscored their fallibility. Despite being certified, drivers can harbor critical vulnerabilities, allowing attackers to subvert system defenses. The exploitation of these vulnerabilities enables attackers to execute malicious code with elevated privileges, posing severe risks to system integrity and data confidentiality.

### 2. Addressing Windows Driver Vulnerabilities

#### 2.1. Evaluation of Microsoft's Response

Microsoft's response to driver vulnerabilities primarily revolves around certification programs such as the *Windows Hardware Quality Labs* (WHQL) and *Extended Validation* (EV). While these programs aim to ensure driver compatibility and security, their effectiveness remains limited. Recent attacks have demonstrated that certified drivers may still harbor vulnerabilities, as Microsoft's certification process does not comprehensively scrutinize driver code for potential flaws.

#### 2.2. Current Research Efforts and Limitations

Despite concerted research efforts, the field of Windows driver vulnerability mitigation faces significant challenges. Existing tools and methodologies, while valuable, often fall short of providing comprehensive solutions. Automated vulnerability assessment tools, such as `Popkorn` and `Screwed Drivers`, offer insights into potential vulnerabilities but lack the capacity to analyze both Windows Driver Model (WDM) and Windows Driver Framework (WDF) drivers effectively.

Current research efforts in vulnerability assessment of Windows drivers have yielded promising developments, yet they also face inherent limitations that warrant attention and consideration. In this section, we delve into the methodologies employed by recent tools and highlight their strengths and limitations.

**TAU Methodology:**

In October 31st, 2023, an article on the VMWare blog introduced `TAU`, a novel tool developed by Takahiro Haruyama aimed at automating the detection of vulnerabilities in WDM and WDF x64 drivers. `TAU` leverages IDA Pro scripts to conduct comprehensive vulnerability hunts, focusing on the unique characteristics of WDF drivers. Despite being open-source, `TAU`'s accessibility is restricted due to its dependency on IDA Pro. However, Haruyama's insights into `TAU`'s functionality have unveiled a distinct methodology for vulnerability hunting in WDF drivers, tailored to address their specific nuances. `TAU` also aims to address the limitations of existing tools like `ScrewedDrivers` by Eclypsium, which primarily target WDM drivers. By filling potential gaps in `ScrewedDrivers'` capabilities, `TAU` represents a significant advancement in vulnerability assessment methodologies for Windows drivers.

**ScrewedDrivers Methodology:**

Eclypsium's `ScrewedDrivers` tool has been instrumental in identifying vulnerabilities in Windows drivers, particularly within the WDM framework. However, its applicability to WDF drivers is limited, as it primarily relies on symbolic execution with Angr, which may lead to "path explosions, false negatives, and other unknown errors." While `ScrewedDrivers` has contributed significantly to driver security, its effectiveness in analyzing WDF drivers remains constrained by the complexities inherent in the framework.

Despite the advancements represented by tools like `TAU` and `ScrewedDrivers`, achieving full automation in vulnerability assessment remains a formidable challenge. Even with `TAU`'s introduction, manual verification remains indispensable due to the intricate nature of WDF drivers and the potential for overlooked vulnerabilities. As the field continues to evolve, researchers and developers must navigate these limitations while striving to enhance the efficacy and reliability of vulnerability assessment methodologies for Windows drivers.

### 3. Strategies for Vulnerability Assessment and Mitigation

#### 3.1. Standards and Challenges in Driver Development

The establishment of standards, such as WHQL and EV, represents a crucial step in mitigating driver vulnerabilities. However, these standards alone are insufficient in addressing the evolving threat landscape. Vulnerabilities persist even in certified drivers, highlighting the need for more rigorous code verification processes and comprehensive security measures.

#### 3.2. Assessing Vulnerability Assessment Methodologies

Current vulnerability assessment methodologies, including `TAU` and `Popkorn`, offer valuable insights into driver vulnerabilities. However, their effectiveness varies, and improvements are necessary to enhance their scope and accuracy. Future efforts should focus on refining analysis techniques and expanding the toolsets to encompass a broader range of drivers, including WDF drivers.

### 4. Advancements in Vulnerability Research Automation

#### 4.1. Current Landscape and Research Gaps

Despite recent advancements, the vulnerability research landscape remains fragmented, with notable gaps in analyzing WDF drivers effectively. Open-source projects like `Popkorn` have shown promise in identifying vulnerabilities but require enhancements to address the complexities of WDF drivers comprehensively. Here is a summary of the state of the art projects:

|                     | **Popkorn** |      **Screwed Drivers**     |  **TAU**  |
|:-------------------:|:-----------:|:----------------------------:|:---------:|
|   **Open Source**   |      ✅      |               ✅              |     ✅     |
|       **Free**      |      ✅      |               ✅              |     ❌     |
|      **Scope**      |     WDM     |              WDM             | WDM + WDF |
| **Frameworks used** |    `Angr`   | `Angr`, `Radare2`, `objdump` | `IDA Pro` |
| **Fully Automated** |      ✅      |               ❌              |     ❌     |

#### 4.2. Advancements in the Popkorn Tool

Ongoing efforts to enhance tools like `Popkorn` involve replicating proof-of-concept (POC) exploits, improving vulnerability detection mechanisms, and expanding the dataset to encompass a broader range of driver types. These advancements aim to bolster the tool's efficacy in identifying and mitigating driver vulnerabilities across diverse Windows environments.

### 4.2. Advancements in the Popkorn Tool

Since its inception, the Popkorn tool has undergone significant enhancements and validations to ensure its reliability and effectiveness in identifying vulnerabilities within Windows drivers. This section highlights the key advancements made to the Popkorn tool, including exact reproduction of results, environment updates, and the utilization of a new dataset.

#### 4.2.1. Exact Reproduction of Results

The initial phase of the project focused on replicating the results presented in the research paper, serving as a benchmark to validate the tool's functionality. The Popkorn tool and dataset were retrieved from the developers' GitHub repository, facilitating an in-depth analysis of its capabilities. The reproduction yielded the following results:

| Metrics                 | Original Test | New Identical Test |
|-------------------------|---------------|--------------------|
| Number of drivers used  | 212           | 271                |
| Unique bugs found       | 38            | 37                 |
| Timeouts                | 60            | -                  |

While the results deviated slightly from the initial expectations, with the number of drivers and unique bugs differing marginally, the tool demonstrated consistent performance across both tests. The discrepancies in results underscored the importance of meticulous validation and testing to ensure the accuracy and reliability of the tool.

#### 4.2.2. Test Environment Update

In response to the evolving technological landscape, the test environment underwent comprehensive updates to maintain compatibility and functionality with the latest software versions. The technical updates included:

- `Ubuntu` updated from `20.04` to `22.04`
- `Python` upgraded from `3.8` to `3.12`
- `Virtualwrapper` replaced by `python3.12-venv`
- Python libraries updated, including `angr`, `ipython`, and `ipdb`

Following the environment updates, the Popkorn tool was subjected to a new test using the updated configuration. The results demonstrated consistency in vulnerability detection, reaffirming the tool's efficacy in identifying potential threats within Windows drivers.

| Metrics                 | Original Test | New Updated Test |
|-------------------------|---------------|------------------|
| Number of drivers used  | 212           | 271              |
| Unique bugs found       | 38            | 38               |

The alignment of results between the original and updated tests underscored the robustness and adaptability of the Popkorn tool to evolving software environments.

#### 4.2.3. Creation of a New Dataset

As part of the ongoing enhancements, a new dataset comprising Windows Driver Framework (WDF) drivers was curated to expand the scope of analysis and address potential blind spots. The dataset collection process involved gathering drivers from the team's computer, focusing on drivers located in `C://Windows/System32/drivers`. The analysis of the new dataset yielded the following insights:

| Metrics                 | Result    |
|-------------------------|-----------|
| Number of drivers       | 454       |
| No sinks found          | 141       |
| Timeouts                | 9         |
| Not vulnerable          | 304       |

The analysis revealed that the majority of drivers within the dataset were deemed safe from vulnerabilities identified by Popkorn. However, the presence of timeouts underscored the need for further investigation and refinement of the analysis methodology, particularly concerning WDF drivers.

The advancements in the Popkorn tool, including exact reproduction of results, environment updates, and utilization of a new dataset, signify a significant stride towards enhancing the efficacy and reliability of vulnerability assessment in Windows drivers. These enhancements pave the way for more robust cybersecurity measures, mitigating potential threats and safeguarding critical systems against evolving vulnerabilities.

#### 4.3. Understanding Limitations and Challenges

Despite notable advancements, automated vulnerability assessment tools face inherent limitations. Challenges include scope limitations, false negatives, and the complexity of detecting vulnerabilities within WDF drivers. Addressing these challenges requires a concerted effort to refine analysis techniques and develop more robust mitigation strategies.

#### 4.4. Proposing Future Improvements

Future improvements in vulnerability research automation should prioritize expanding the scope of analysis to include WDF drivers, enhancing detection capabilities, and refining analysis methodologies. Additionally, efforts to incorporate advanced techniques, such as identifying potential BYOVD attacks, are essential for safeguarding against emerging threats effectively.

# Conclusion

In conclusion, addressing Windows driver vulnerabilities demands a multifaceted approach encompassing stringent standards, advanced research methodologies, and continuous innovation in vulnerability assessment tools. By understanding the nuances of driver security threats and leveraging cutting-edge technologies, the cybersecurity community can mitigate risks and fortify systems against emerging threats effectively.

---

We hope that this article has provided valuable insights into the intricate landscape of Windows driver security and the methodologies employed in vulnerability research. Should you wish to delve deeper into our research findings or have further inquiries, we encourage you to explore our full research article available to download below.

[click here to downlad research paper](/files/research-paper-reverse-windows-drivers.pdf)

> For additional information or inquiries, please do not hesitate to reach out to us via the contact form available [here](https://garnajee.github.io/contact). We welcome your feedback, questions, and collaboration opportunities.

Thank you for your interest and support.

### Acknowledgments

We would like to express our sincere gratitude to Mr. Guillet, our project supervisor, for his invaluable guidance and support throughout the duration of this project. His expertise, encouragement, and unwavering commitment have been instrumental in shaping our research journey.

We extend our heartfelt appreciation to the experts at DGA-MI for their insightful contributions, diligent guidance, and unwavering support throughout the course of our research. Their expertise, feedback, and encouragement have been invaluable assets that have enriched our project and propelled us forward.

