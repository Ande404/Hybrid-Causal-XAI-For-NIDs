## **🎯 The Big Picture: What Did We Just Do?**

Think of our network like a **crime scene investigation**. Traditional AI (your LSTM) is like a detective who says *"I'm 95% sure this person is guilty"* but can't explain why. Your causal graph is like **reconstructing the chain of events** that led to the crime.

---

## **📊 Understanding the Results**

![image representing final casual graph](/step2_causal_discovery/final_causal_graph.png "final_casual graph")

### **Image 1: Final Causal Graph (20 edges)**

This is your **"cause-and-effect map"** of network attacks. Let's decode it:

#### **The Colors Mean:**

* 🟡 **Yellow (Label)**: The outcome \- "Is this alert important or not?"  
* 🔵 **Light Blue (SOC Expert Features)**: Features that security experts said are most important  
  * SignatureID, SignatureMatchesPerDay, SignatureIDSimilarity, Similarity, SCAS  
* 🟢 **Green (Additional Features)**: Other network features we added  
  * Proto, IntPort, ExtPort, AlertCount, ProtoSimilarity

#### **The Arrows Mean:**

An arrow A → B means **"A causes B"** or **"A influences B"**

**Real-World Analogy**: Think of dominoes falling:

* When you knock over the first domino (cause), it makes the next one fall (effect)  
* The arrows show which dominoes knock over which

---

### **Key Findings from Your Final Graph:**

#### **1\. Root Causes (Nodes with NO incoming arrows)**

These are the **"first dominoes"** \- features that START the causal chain:

* **Proto** (Protocol type \- TCP/UDP)  
* **ExtPort** (External port number)

**Analogy**: These are like the **weather conditions** that enable a forest fire. They don't directly cause the fire, but they set the stage.

#### **2\. Central Hub: SignatureIDSimilarity**

This node has MANY connections \- it's a **causal hub**.

**What it means**: This feature is influenced by many things AND influences many things. It's like a **traffic intersection** where multiple roads meet.

**Causal Chain Example**:

Proto → SignatureIDSimilarity → Label

Translation: The network protocol influences how similar signatures look, which then affects whether an alert is important.

#### **3\. Direct Causes of "Label" (Important Alert)**

These features DIRECTLY cause an alert to be classified as important:

From your graph:

* SignatureMatchesPerDay  
* SignatureIDSimilarity  
* SCAS  
* IntPort  
* AlertCount  
* ProtoSimilarity  
* Similarity

**Analogy**: These are like the **immediate suspects** at a crime scene \- they're directly connected to the outcome.

---

![causual algorithm graph comparison](/step2_causal_discovery/causal_graphs_comparison.png "PC vd GES algorithms compared")

### **Image 2: PC vs GES Comparison**

This shows the difference between your two causal discovery algorithms:

#### **PC Algorithm (Left): 42 edges**

* More **aggressive** \- found more causal relationships  
* Think of it as a detective who sees connections everywhere

#### **GES Algorithm (Right): 29 edges**

* More **conservative** \- only shows strong causal relationships  
* Think of it as a detective who only reports solid evidence

#### **Your Final Graph: 20 edges**

This is the **consensus** \- only the relationships BOTH algorithms agreed on.

**Analogy**: Like two witnesses describing a crime. Your final graph includes only what BOTH witnesses confirmed.

---

## **🔍 What Makes This Novel**

### **Traditional XAI Says:**

"This alert was flagged because SCAS \= 0.85 (high importance)"

**Problem**: This is just correlation. It's like saying *"All criminals wear shoes, so shoes cause crime"*

### **Your Hybrid Approach Says:**

"This alert was flagged because SCAS \= 0.85, BUT the root cause is actually SignatureMatchesPerDay → Proto → SignatureIDSimilarity → SCAS → Label"

**Value**: You've identified the **ROOT CAUSE** (SignatureMatchesPerDay) that started the chain reaction.

---

## **📈 Interpreting Specific Causal Paths**

 **real examples** from our graph:

### **Example 1: The Signature Scanning Attack**

SignatureMatchesPerDay → SCAS → Label

**Translation**:

* High frequency of signature matches (SignatureMatchesPerDay)  
* → Causes elevated SCAS scores  
* → Which causes the alert to be classified as "Important"

**Real-World**: This suggests **coordinated scanning campaigns** rather than isolated anomalies.

**Actionable Insight**: "Block the source and investigate OTHER hosts with similar SignatureMatchesPerDay patterns"

---

### **Example 2: Protocol-Based Attack**

Proto → SignatureIDSimilarity → Label

**Translation**:

* The network protocol type (TCP vs UDP)  
* → Influences how signature patterns look  
* → Which affects alert classification

**Real-World**: Certain protocols (like UDP) are more commonly used in DDoS attacks, making their signatures more similar to known attack patterns.

---

### **Example 3: Port-Based Exploitation**

IntPort → SignatureIDSimilarity → Label

**Translation**:

* Internal port numbers  
* → Affect signature pattern matching  
* → Which determines alert importance

**Real-World**: Attacks on common ports (80, 443, 22\) have well-known signatures, making them easier to classify.

---

## **🎤 Key Talking Points**

### **Slide 1: The Problem**

*"Imagine a smoke detector that beeps but won't tell you WHERE the fire is or HOW it started. That's current NIDS systems."*

### **Slide 2: Our Solution**

*"We built a system that not only detects the fire (LSTM), explains why it thinks there's a fire (XAI), but ALSO traces back to the match that started it (Causal Discovery)."*

### **Slide 3: The Causal Graph**

*"This graph is like a family tree of network attacks. It shows which features are 'parents' (causes) and which are 'children' (effects)."*

**Point to the graph:**

* "Yellow node \= The verdict (important or not)"  
* "Arrows \= Cause-and-effect relationships"  
* "Blue nodes \= Features security experts told us to watch"  
* "The path from SignatureMatchesPerDay to Label shows the ROOT CAUSE of attacks."

### **Slide 4: Why This Matters**

*"Instead of just saying 'SCAS is high, therefore attack', we can now say 'SCAS is high BECAUSE SignatureMatchesPerDay spiked, suggesting a coordinated scanning campaign. Recommended action: Block source IP and audit similar patterns.'"*

---

## **📊 Important metrics**

From our results:

* **PC Algorithm**: Found 42 causal relationships  
* **GES Algorithm**: Found 29 causal relationships  
* **Consensus**: 20 relationships both agreed on (these are your most reliable findings)  
* **Root Causes**: 2 features (Proto, ExtPort) have no incoming edges \- these START causal chains  
* **Direct Causes of Alerts**: 7 features directly influence whether an alert is important

---

## **🤔 Answers to Tough Questions**

**Q: "How do you KNOW these are real causal relationships?"**

**A**: "We used two different algorithms (PC and GES) and only kept relationships both agreed on. We also applied domain knowledge \- for example, we forbade illogical relationships like 'port number causes signature ID'. Additionally, these relationships align with what security experts at TalTech SOC told us."

**Q: "What's the advantage over just using XAI?"**

**A**: "XAI tells you WHAT the model looked at. Causal analysis tells you WHY those features are important and HOW they relate to each other. It's the difference between 'this patient has a fever' vs 'this patient has a fever BECAUSE of a bacterial infection that started from an open wound.'"

---

## **🎯 The "So What?" Moment**

**For SOC Analysts:** Instead of getting 1,000 alerts saying "possible attack", they get:

"Alert: Coordinated scanning detected  
 Root Cause: SignatureMatchesPerDay \= 140K (abnormal)  
 Causal Chain: High match rate → Elevated SCAS → Alert  
 Action: This indicates campaign-level activity. Recommended: Block IP, investigate correlated hosts"

**This is ACTIONABLE** because it tells them:

1. WHAT was detected (scanning)  
2. WHY it was flagged (causal chain)  
3. WHAT TO DO about it (block and investigate)

---


### **Understanding Causal Discovery Algorithms**

To build this causal map, the senior researcher hires two specialists—each with a different method for untangling the complex relationships between all the factors (virus, cough, fever, smoking habits, etc.).

### **\#\# PC Algorithm: The Methodical Detective 🕵️‍♀️**

The PC (Peter-Clark) algorithm works like a detective who uses a **process of elimination**.

1. **Start with a Messy Corkboard**: The detective assumes *everything might be connected*. He draws a string between every single factor on his board: Virus ↔ Cough, Cough ↔ Fever, Smoking ↔ Virus, etc. It's a complete web of possibilities.  
2. **Run Tests to Snip Strings**: The detective then systematically tests each connection to see if it can be ruled out. He asks questions like, "Looking only at patients who have the virus, does having a fever make a cough any more likely?" If the answer is no, he concludes that the fever and cough don't cause each other directly; they are both just symptoms of the virus. **\*snip\*** He cuts the string between fever and cough.  
3. **Reveal the Causal Map**: After running hundreds of these statistical elimination tests, the only strings left on the board are the ones representing the strongest, most likely direct causal relationships.

---
### \#\# GES Algorithm: The master sculptor ⚒️ 
the **GES (Greedy Equivalence Search) algorithm** is like a **master sculptor** creating a statue in two distinct, powerful phases.



###  The Analogy: The Master Sculptor

The sculptor's goal is to create the perfect statue (the best causal map) from a block of marble. The "quality score" is how lifelike and accurate the final statue is.

### Phase 1: Forward Search (Roughing It Out with Clay)

Instead of making tiny, one-at-a-time changes, the sculptor starts by rapidly adding big chunks of clay to a wireframe. At each step, she considers all possible additions and chooses the **single best chunk** that adds the most form and realism to her statue (i.e., the one that provides the biggest "greedy" improvement to the quality score).

She continues this process, always adding the most impactful piece, until she has a complete but somewhat overly complex rough draft of the statue.

* **In algorithm terms**: This is the "forward phase," where GES greedily **adds edges** (connections) to the causal map, always choosing the single edge that most improves the model's score, until no more additions can improve it.

---

### Phase 2: Backward Search (Refining with a Chisel)

Now, the sculptor puts down the clay and picks up her chisel. Looking at her detailed rough draft, she begins to refine it. She considers all possible pieces she could remove. At each step, she makes the **single best chisel** that removes an unnecessary detail and most improves the statue's lifelike quality.

She continues this "greedy" removal process until any further chiseling would harm the statue.

* **In algorithm terms**: This is the "backward phase," where GES takes the complex map from phase one and greedily **removes edges**, always choosing the single edge whose removal most improves the score.

---

### **\#\# Why This Two-Phase Approach is Better**

By first building up a model and then pruning it down, GES can explore the possible causal structures much more efficiently and is less likely to get stuck on a simple "hill" than the standard Hill Climb algorithm. It has a better chance of finding the true "mountain" (the globally optimal causal map).

---
## **📋 Dataset Feature Definitions with Analogies**

### **1\. SignatureMatchesPerDay**

**What it is**: Average number of times per day that this specific signature (attack pattern) has been triggered.

**Simple Explanation**: How frequently this type of alert shows up daily.

**Analogy**: Like a burglar alarm going off. If it rings once a month \= probably real threat. If it rings 10,000 times a day \= probably a false alarm or someone testing the system.

**Example Values**:

* Low (100/day) \= Rare signature, possibly serious  
* High (140,000/day) \= Very common, might be scanning or noise

**Why it matters**: Rare signatures often indicate targeted attacks. Super frequent ones might be automated scanning or false positives.

---

### **2\. SignatureIDSimilarity**

**What it is**: How similar the current alert's signature is to other alerts in the same cluster or group.

**Simple Explanation**: Does this alert look like other alerts we've seen?

**Analogy**: Like facial recognition. If a suspect's face matches 90% with known criminals in the database, that's high similarity. If it only matches 10%, they might be a new threat.

**Values**: 0 to 1 (or 0% to 100%)

* 0 \= Unique, never seen before  
* 1 \= Identical to known patterns

**Why it matters**:

* High similarity \= Part of known attack campaign  
* Low similarity \= New/unknown threat (might be more dangerous OR a false positive)

---

### **3\. SCAS (Stream Clustering Algorithm Score)**

**What it is**: A score from the Customized Stream Clustering Algorithm for Suricata that determines if an alert is an outlier or fits with normal patterns.

**Simple Explanation**: Is this alert behaving like the crowd, or is it a weird outlier?

**Analogy**: Imagine a flock of birds flying. SCAS measures if this bird is:

* Flying with the flock (inlier) \= 0 \= Normal behavior, probably irrelevant  
* Flying alone in a weird direction (outlier) \= 1 \= Suspicious, possibly important

**Values**:

* 0 \= Inlier (fits normal pattern cluster)  
* 1 \= Outlier (doesn't fit any cluster)

**Why it matters**: Outliers often indicate new or evolving threats that don't match historical patterns.

---

### **4\. IntPort (Internal Port)**

**What it is**: The port number on the INTERNAL network (TalTech's network) where the alert occurred.

**Simple Explanation**: Which door/entrance in your building was the suspicious activity detected at?

**Analogy**: Your house has different doors:

* Port 80/443 (HTTP/HTTPS) \= Front door (web traffic)  
* Port 22 (SSH) \= Back door (admin access)  
* Port 3389 (RDP) \= Service entrance (remote desktop)  
* Random high port (50000+) \= Suspicious window nobody should use

**Common Important Ports**:

* 22 \= SSH (hackers love this)  
* 80/443 \= Web traffic  
* 3389 \= Remote Desktop (ransomware target)  
* 1433/3306 \= Databases (data theft target)

**Why it matters**: Attacks on critical ports (SSH, databases) are more serious than attacks on random ports.

---

### **5\. AlertCount**

**What it is**: Number of individual alerts in this alert group.

**Simple Explanation**: How many times did this exact same thing happen in a short time window?

**Analogy**: Like a store's security camera. If it detects:

* 1 person entering \= Normal  
* 50 people entering in 5 minutes \= Flash mob or robbery

**Values**:

* Low (1-5) \= Isolated incident  
* Medium (10-100) \= Repeated behavior  
* High (1000+) \= Flood/DDoS/scanning campaign

**Why it matters**:

* Single alerts might be mistakes  
* Repeated alerts indicate persistent threats or automated attacks

---

### **6\. ProtoSimilarity**

**What it is**: How similar the protocol (TCP/UDP/ICMP) of this alert is to other alerts in the cluster.

**Simple Explanation**: Are attackers using the same communication method?

**Analogy**: Like language similarity. If 10 suspicious people all speak Russian, that's high similarity (coordinated group). If they each speak different languages, that's low similarity (unrelated events).

**Protocol Types**:

* TCP (Protocol 6\) \= Reliable, connection-based (like a phone call)  
* UDP (Protocol 17\) \= Fast, connectionless (like shouting)  
* ICMP (Protocol 1\) \= Network diagnostics (like sonar pings)

**Values**: 0 to 1

* 1 \= All alerts use same protocol (coordinated)  
* 0 \= Different protocols (random/unrelated)

**Why it matters**: Attackers in a campaign usually stick to one protocol. High similarity suggests organized attack.

---

### **7\. Similarity (Overall Similarity)**

**What it is**: Overall similarity of this alert to other alerts in the same cluster or to other outlier alerts.

**Simple Explanation**: How much does this alert look like its "neighbors"?

**Analogy**: Like a detective asking "Does this crime scene look like the other crime scenes?"

* High similarity \= Serial crime (same attacker, same method)  
* Low similarity \= Different attacker or method

**Values**: 0 to 1

* 0 \= Completely unique  
* 1 \= Identical to cluster patterns

**Calculation**: Combines similarity across multiple dimensions:

* Source/destination IPs  
* Ports  
* Protocols  
* Signature patterns  
* Time patterns

**Why it matters**:

* High similarity \= Part of known attack campaign (easier to handle)  
* Low similarity \= New threat or false positive (needs investigation)

---

## **🔗 How They Work Together (Real Example)**

Let me give you a **real attack scenario** using all these features:

### **Scenario: Coordinated SSH Brute Force Attack**

* SignatureMatchesPerDay \= 5,000  
*   ↓ (This SSH attack signature is triggering a lot)  
*     
* IntPort \= 22 (SSH)  
*   ↓ (Attackers targeting the SSH port)  
*     
* AlertCount \= 250  
*   ↓ (250 failed login attempts in 5 minutes)  
*     
* ProtoSimilarity \= 1.0  
*   ↓ (All attempts use TCP protocol \- coordinated)  
*     
* SignatureIDSimilarity \= 0.95  
*   ↓ (95% similar to known SSH brute force patterns)  
*     
* Similarity \= 0.90  
*   ↓ (90% similar to other alerts in this cluster)  
*     
* SCAS \= 1 (outlier)  
*   ↓ (But the VOLUME is unusual \- it's an outlier)  
*     
* → RESULT: Important Alert\! 🚨

**Translation**: "Multiple coordinated SSH login attempts (high AlertCount) on a critical port (IntPort=22), using identical methods (high ProtoSimilarity and SignatureIDSimilarity), at an unusual scale (SCAS=1 outlier). This is a brute force attack campaign."

---

## **📊 Quick Reference Table**

| Feature | Range | Good/Safe | Bad/Suspicious |
| ----- | ----- | ----- | ----- |
| **SignatureMatchesPerDay** | 0 \- 200K | 100-1000 (rare) | \>100K (too common \= noise) OR \<10 (ultra-rare \= targeted) |
| **SignatureIDSimilarity** | 0 \- 1 | 0.7-0.9 (known pattern) | \<0.3 (unknown) or 1.0 (exact repeat) |
| **SCAS** | 0 or 1 | 0 (inlier, normal) | 1 (outlier, unusual) |
| **IntPort** | 1 \- 65535 | \>1024 (random) | 22, 80, 443, 3389, 1433 (critical services) |
| **AlertCount** | 1 \- ∞ | 1-5 (isolated) | \>100 (flood/attack) |
| **ProtoSimilarity** | 0 \- 1 | 0.5 (varied) | 1.0 (coordinated) |
| **Similarity** | 0 \- 1 | 0.6-0.8 (related) | \<0.3 (outlier) or \>0.95 (exact repeat) |

---

## **🎤 For Your Presentation**

When explaining these in your presentation, use this simple framework:

**"Think of network security like airport security:**

* **SignatureMatchesPerDay** \= How often this type of suspicious behavior is seen (daily bag searches)  
* **SignatureIDSimilarity** \= Does this bag look like other suspicious bags?  
* **SCAS** \= Is this person acting differently from everyone else in line?  
* **IntPort** \= Which terminal/gate are they trying to access? (Gate 22 \= restricted area)  
* **AlertCount** \= How many times did they trigger the metal detector?  
* **ProtoSimilarity** \= Are they using the same tactics as other suspicious people?  
* **Similarity** \= Overall, how suspicious is this compared to other incidents?"
