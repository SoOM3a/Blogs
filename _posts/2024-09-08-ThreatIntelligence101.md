---
title: Threat Intelligence 101
date: 2024-09-08 08:25:41 AM
categories: [Threat Intelligence]
tags: [ThreatIntelligence101] 
render_with_liquid: false
media_subpath: /assets/img/ThreatIntelligence/ThreatIntelligence101
authors: [0]
---

## Threat Intelligence

Cyber threat intelligence results from disparate cyber threat data collected, processed, and analyzed to provide insights into cybercriminals’ motives, targets, and methodologies. Because cyber threat intelligence delivers accurate, timely, and relevant information, it can be used to identify existing or emerging security incidents reliably.

### CTI Types

Mainly their are 3 levels of threat intelligence, but some organization makes them 4 but you cam comsider technical part of operational threat intelligence (Operational or technical).
![CTI Types](cti_types_sans.png)

#### Term of usage

![CTI Types](cti_types.png)

### Traffic Light Protocol

> The Traffic Light Protocol (TLP) was created in order to facilitate greater sharing of information. TLP is a set of designations used to ensure that sensitive information is shared with the _appropriate audience_.

![traffic Light Protocol](tlp.png)


### CTI lifecycle
![CTI Lifecyle](cti_lifecylce.png)


### Phase 1: Planning 
you will define the goals and objectives of your threat intelligence activity or plan out your entire program’s ambitions. n addition, it is important to consider the audiences and consumers of the information. Taking time up-front to plan cyber threat intelligence activities ensures that the results will be useful and make the most of valuable resources.

#### Tools
- SAT (Straucture analysis, ex: mindmup) , DAT (data analytics, ex:excel)
- Defense scale 
![Defense scale](defense_scale.png)
- Build your Threat Model

### Phase 2: Collection 
When gathering raw data, it is best to tap into a broad range of internal and external sources that meet the criteria established in the planning phase. Sources of data to support a threat intelligence _lifecycle include_:

- Data from the open web and the dark web
- Information from news sources and social media
- Network event logs (NetFlow logs)
- Records of past incident responses
- Third-party intelligence
- Phising Emails
- malicious Links/Hashes
- Network Pivioting (proxy)
- Endpoint activities (hard-disk, memory, network)
- Endpoint connection piviointing 
- TLS Pivoiting (Maltego)
- OSINT
    - Aggregation and pivioting in excel 
    - Domain Pivoting (domaintools Iris)
    - VT and alienvault for Ip-intellignet (Automater.py IP)
- Massive information OSINT
    - Recorded Future

> The information you collect needs to be stored somewhere. This can be a simple storage solution like an Excel spreadsheet or a specialist threat intelligence platform. Popular open-source threat intelligence platforms include the Malware Information Sharing Platform (MISP) and OpenCTI. There are also commercial platforms commonly called Threat Intelligence Platforms (TIPs), like The Record Future Cloud.
{: .prompt-tip }
 
### Phase 3: Processing

With raw data collected, you need to process it to transform it into information for analysis. Welcome to the _Processing stage_. 

- **Cleaning** the data to remove any garbage.
- **Normalizing** the data so it can be uploaded to your data storage solution.
- **Verifying** the data is legitimate and trustworthy based on the source you collected it from and what others are saying about it. For instance, is an IP address recognized as malicious by multiple intelligence vendors or no one outside the blog post you found it on?

### Phase 4: Analysis
The Analysis stage turns the information you have gathered into actionable intelligence that your organization can use to defend itself or inform its strategic decisions. It involves a human operator using their knowledge and experience to identify patterns or trends in the information collected. Once identified, these patterns are interpreted to produce insights the organization can use to inform decisions.


#### Tools and Requirment for Analysis 

- SAT (Straucture Analysis Technique) [**Mindmap**](https://www.mindmup.com/)
- Cyber Kill Chain
![Cyber Kill Chain](kc.png)
    - Recap course on action matrix (COA)
    ![Diamond Model](coa_matrix.jpg) 

- Dimond model
![Diamond Model](diamond_model.jpg)

> After analysis for dimond model, you should have somthing like the following 
![Diamond Model Fin7](fin7_dm.webp)
So, we can combine both models Diamond Model * KC. The result would be somthink like 
![Diamond Model mixed with KC](CTI-KC.webp)
Exmaple of the report in [**Netskope**](https://www.netskope.com/blog/strategies-for-gathering-and-contextualizing-cyber-threat-intelligence)
{: .prompt-tip }

> **Note:** Always remember that in CTI, we go with KC in reverse order to find successfull chain
{: .prompt-info }

#### [**Fallacies in Reports**](https://kravensecurity.com/cti-analysis-bias/)
- **menu of logical fallacies (flaws in the argument)**
    - **Anecdotal fallacy** 
        - **Fallacy:** An argument based on personal experience, anecdotes, or isolated examples rather than factual evidence or logical argument. For instance, “It happened to me; therefore, it must happen to everyone, or it must be true.” Personal experiences are subjective and lead to overgeneralizations that don’t consider contradictory evidence or the broader context.
        Example: “I analyzed the intrusion and didn’t see anything suspicious, so you must be wrong.”

    - **Appeal to probability**
        - **Fallacy:** An argument that assumes that something will definitely happen because something is likely to occur. This fallacy overlooks the distinction between possibility and certainty, leading to flawed conclusions.
        Example: “Russia does a lot of cyber attacks against the finance industry. It must be Russia who attacked us.”

    - **Appeal to the Stone**
       - **Fallacy**: When you dismiss an argument as absurd or untrue without providing any evidence or logical reasoning to dismiss it. You are relying on the assertion that the argument is absurd rather than justifying why it should be rejected.
        Example: “It is absurd to think a Western country would spy on its own citizens.”
    - **Argument from Silence** 
        - **Fallacy:** Accepting a conclusion due to the lack of evidence against it. This is problematic because a lack of evidence could have many interpretations (e.g., it hasn’t been found yet, there is no way to prove it with evidence, the evidence is being held back, etc.)
        Example: “I have proof it wasn’t China that attacked us, and no proof that was not North Korea, so it must’ve been North Korea.”

    - **Argument from Repetition** 
        - **Fallacy**: When an argument or claim is repeated over and over again with the hope that it will just be accepted as being valid. You’ve probably experienced this one with your partner.
        Example: “We’ve been looking at this data all day. Let’s just say Russia did it.”
    - **Burden of proof**
        - **Fallacy:** The person who makes an argument or claim must prove its validity. It is not up to someone to disprove your assessment. Instead, you must prove it with supporting evidence.
        Example: “Prove that it wasn’t Iran that hacked us.”
    - **Middle Ground**
        - **Fallacy:** The erroneous belief that the truth must lie between two extremes or opposing views. You accept that a “middle ground” between two arguments must be the most valid without considering the evidence or validity of each individual argument.
        Example: “You think it is a cybercrime gang from Russia. I think it is a cybercrime gang from China. It must be a cybercrime gang.”
- **Menu of Cognitive Biases (flwas in thinking)**
    - **Anchoring**
        - **Cognitive bias:** Where you rely too heavily on the first piece of information you receive, you are “anchored” on it. This prevents you from seeking new information or analyzing competing information. It often happens when you find something of value at first, so you keep pulling on that information rather than looking for other pieces of information.
        - **Example:** “This IP address is from India so it must be an Indian threat actor I am looking for.”
    - **Confirmation Bais**
        - **Cognitive bias:** The tendency to favor information confirming our pre-existing beliefs while dismissing or ignoring evidence contradicting those beliefs. This can lead to putting greater significance on certain pieces of evidence, undervaluing valid evidence, or failing to consider other hypotheses (e.g. congruence bias – a form of confirmation bias).
        - **Example:** “Focusing on just the fact an IP address was from Russia, even though the TTPs used during an attack align with those of Iran.”
    - **Congruence**
     the tendency to test hypotheses exclusively through direct testing, instead of testing possible alternative hypotheses. 
    - **Hindsight**
        - **Cognitive bias:** The tendency to perceive past events as more predictable than they actually were. For instance, an unlikely outcome is perceived as “obvious” after it has occurred. Predicting cyber threats is very difficult. Hindsight bias inappropriately simplifies that problem and diminishes the rigorous work your CTI team applies to resolve an ambiguous situation.
        Example: “Of course, it was the Russians. They always attack the US.”        
    - **Illusory Correlation** 
        - **Cognitive bias:** Perceiving a relationship between two events or pieces of information when no such relationship exists. This is common when you discover two unusual observations.
        - **Example:** “There is no way the data was leaked accidentally. It must have been a targeted attack from a nation-state.”

### Phase 5: Dissemination
Once you have turned the collected data into actionable intelligence, it is time to share it with the relevant stakeholders. 
This is one of the key stages in the threat intelligence lifecycle that you want to get right. You may have the best intelligence in the world, which may save your company from an imminent attack or steer your cyber security program back on course. However, it is useless if you fail to communicate this information to the right stakeholders in a timely manner.

> Try to make the dissemination stage as automated as possible. Automatically upload IOCs for detection to your SIEM or EDR platform, use pre-defined templates when writing emails, reports, or presentations, and create information-sharing groups so you don’t need to contact every stakeholder individually. 
{: .prompt-tip }

### Phase 6: Feedback
An often neglected stage of the threat intelligence lifecycle is the Feedback stage. Every CTI analyst is eager to collect, analyze, and share intelligence. They are less eager to receive feedback on the effectiveness of the efforts or if the intelligence they are consuming/producing is any good. 

The Feedback stage is crucial if you want to see your CTI work continuously improve and add value to your organization. You need to know if your CTI efforts are making a difference to your organization at the operational or strategic level and, if not, how to adjust things to ensure they do.  

Too often, CTI teams will blindly share intelligence with people in their organization and never stop to consider if what they are sharing adds value. You need a two-way communication channel between the CTI team and key stakeholders. The stakeholders need to provide feedback on the effectiveness of the intelligence being shared with them, how they use it, and if any improvements can be made to aid them in making informed decisions.

<script src="https://giscus.app/client.js"
        data-repo="SoOM3a/Blogs"
        data-repo-id="R_kgDOLebVZA"
        data-category="General"
        data-category-id="DIC_kwDOLebVZM4Cd9IX"
        data-mapping="url"
        data-strict="1"
        data-reactions-enabled="1"
        data-emit-metadata="1"
        data-input-position="top"
        data-theme="preferred_color_scheme"
        data-lang="en"
        data-loading="lazy"
        crossorigin="anonymous"
        async>
</script>