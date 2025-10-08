# **Foundational knowledge**:

## **What does LTSM even mean?**

An LSTM (Long Short-Term Memory) is a special type of neural network designed to recognize patterns in sequences of data, like text, speech, or in our case, features of a network alert. It's special because it has a "memory" that allows it to remember important information for a long time.

---

### ** The Analogy: Reading a Novel**

Imagine trying to understand a novel. To do that, you need to remember characters, plot points, and relationships from previous chapters.

#### **A Standard Neural Network: Reading One Word at a Time**

A standard neural network is like someone who reads **only one word** of the book. If they see the word "Harry," they have no idea if it's Harry Potter or Prince Harry. They lack context because they have no memory of the other words.

#### **A Basic Recurrent Neural Network (RNN): A Reader with Bad Memory**

A basic RNN is like a reader who remembers **only the last few words** they read. They can understand a short phrase like "Harry picked up the," and guess the next word might be "wand."

But if they read a long sentence like, "Harry, who grew up on a farm in rural France where he learned to speak fluent French, picked up the...", by the end of the sentence, they've already forgotten the subject was "Harry." This is the "vanishing gradient problem"—early information fades away quickly.

---

### **\#\# The LSTM: An Attentive Reader with Highlighters and Sticky Notes**

The LSTM is designed to solve this memory problem. It's like an attentive reader who has tools to manage their memory as they read through the novel.

The core of the LSTM is its **Cell State**, which you can think of as the book's long-term "plot summary" that the reader keeps in their head. As the LSTM reads each new word (or piece of data), it uses three "gatekeepers" to carefully manage this summary:

1. **The Forget Gate 🗑️** This gate reads the new word and looks at the existing plot summary. It decides, "Is the information from the first chapter about the character's childhood still relevant to this new action scene? Probably not." It then **discards or "forgets" the irrelevant old information**.  
2. **The Input Gate ✍️** This gate reads the new word and decides what new information is important enough to keep. "Aha, this new character is revealed to be a double agent\! That's a huge plot twist." It then **updates the plot summary with this new, critical information**.  
3. **The Output Gate 🗣️** This gate decides what to do *right now*. Based on the current plot summary and the word it's currently reading, it makes a prediction or decision. "Based on my memory that the character is a double agent, and the fact they are now holding a mysterious package, I predict they are about to betray the hero."

By using these gates to selectively forget, add, and use information, the LSTM can maintain a useful memory over very long sequences. It can connect events from Chapter 1 to a climax in Chapter 20, making it incredibly powerful for understanding context.

In our project, the LSTM was 'reading' the sequence of features from a NIDS alert. It used its memory to decide which patterns were crucial for flagging an alert as "important," even if the most telling features weren't right next to each other.

# **What do the XAI algorithms do, and what do they even mean?**

Imagine our trained LSTM model is a brilliant, but very secretive, **Hiring Manager**. She can look at any resume (a NIDS alert) and instantly decide "Hire" (important) or "No Hire" (irrelevant). Your job is to figure out *why* she made a specific decision. You hire four different investigators, each with a unique method.

---

### **The Scenario**

* **The Hiring Manager:** Your trained LSTM model.  
* **The Resume:** A single NIDS alert with all its features.  
* **The Decision:** "Important" or "Irrelevant".  
* **The Goal:** Find out which parts of the resume (e.g., `SignatureID`, `SCAS`) were most influential.

---

### **\#\# LIME: The Local Detective 🕵️**

The LIME investigator is clever but works on a budget. To understand why a candidate was hired, he doesn't try to understand the manager's entire brain.

Instead, he creates a bunch of **slightly altered, fake resumes** right around the real one. He'll change one or two things (e.g., "What if the university was different?" "What if they had 4 years of experience instead of 5?"). He shows these fakes to the manager and watches to see if her decision flickers.

Based on this simple experiment, he builds a **simple, local approximation** of the manager's rule for *this specific candidate*, saying, "It seems the high GPA and the specific skill in Python were the deciding factors for resumes like this one."

* **Approach**: Creates local perturbations (fake data) to build a simple, understandable model that mimics the complex model's behavior in that one spot.  
* **Key Trait**: It's a **fast approximation** that works on any "black box" model, but it's only valid for that single, local decision.

---

### **\#\# SHAP: The Fair-Play Commissioner ⚖️**

The SHAP investigator is obsessed with fairness and game theory. He wants to find the *exact* and fair contribution of every single line on the resume.

His method is incredibly thorough. He imagines a game where you start with a blank resume and add the real candidate's qualifications one by one. He tries **every possible order and combination** of adding these features. He measures how much each feature changes the final "Hire" probability, on average, across all these combinations.

At the end, he provides a definitive report: "After considering all possibilities, the '5 years of experience' contributed \+40% to the decision, the 'degree from MIT' contributed \+35%, and the 'typo in the cover letter' contributed \-15%."

* **Approach**: Uses Shapley values from game theory to fairly distribute the "payout" (the final prediction) among all the "players" (the features).  
* **Key Trait**: It's the **most mathematically fair and consistent** method, but calculating every combination can be very slow and computationally expensive.

---

### **\#\# DeepLIFT & Integrated Gradients: The Brain Scanners 🧠**

These two investigators are neuroscientists who have direct access to the Hiring Manager's brain (the neural network). They don't need to use fake resumes because they can see the neurons firing.

Their method is to trace the manager's thought process.

1. They start with a baseline—a completely **blank or average resume**.  
2. They then watch how the manager's "hiring score" changes as they morph that blank resume into the real candidate's resume.  
3. The parts of the resume that cause the **biggest "jumps" or "spikes" in neural activity** are flagged as the most important.  
* **Integrated Gradients (IG)** does this by following a smooth, straight path from the blank resume to the real one and adding up all the small changes in brain activity along the way.  
* **DeepLIFT** does something similar but compares the neuron activations of the real resume directly to the activations caused by the blank/reference resume.  
* **Approach**: They trace the gradients (the flow of information) directly through the neural network to see how the input features connect to the output.  
* **Key Trait**: They are **powerful and precise** because they look *inside* the model. This is why DeepLIFT performed so well in your test. However, they are model-specific and can only be used on neural networks.

### **Understanding Causal Discovery Algorithms**

To build this causal map, the senior researcher hires two specialists—each with a different method for untangling the complex relationships between all the factors (virus, cough, fever, smoking habits, etc.).

### **\#\# PC Algorithm: The Methodical Detective 🕵️‍♀️**

The PC (Peter-Clark) algorithm works like a detective who uses a **process of elimination**.

1. **Start with a Messy Corkboard**: The detective assumes *everything might be connected*. He draws a string between every single factor on his board: Virus ↔ Cough, Cough ↔ Fever, Smoking ↔ Virus, etc. It's a complete web of possibilities.  
2. **Run Tests to Snip Strings**: The detective then systematically tests each connection to see if it can be ruled out. He asks questions like, "Looking only at patients who have the virus, does having a fever make a cough any more likely?" If the answer is no, he concludes that the fever and cough don't cause each other directly; they are both just symptoms of the virus. **\*snip\*** He cuts the string between fever and cough.  
3. **Reveal the Causal Map**: After running hundreds of these statistical elimination tests, the only strings left on the board are the ones representing the strongest, most likely direct causal relationships.

---

the **GES (Greedy Equivalence Search) algorithm** is like a **master sculptor** creating a statue in two distinct, powerful phases.

---

### **\#\# The Analogy: The Master Sculptor**

The sculptor's goal is to create the perfect statue (the best causal map) from a block of marble. The "quality score" is how lifelike and accurate the final statue is.

### **\#\# Phase 1: Forward Search (Roughing It Out with Clay)**

Instead of making tiny, one-at-a-time changes, the sculptor starts by rapidly adding big chunks of clay to a wireframe. At each step, she considers all possible additions and chooses the **single best chunk** that adds the most form and realism to her statue (i.e., the one that provides the biggest "greedy" improvement to the quality score).

She continues this process, always adding the most impactful piece, until she has a complete but somewhat overly complex rough draft of the statue.

* **In algorithm terms**: This is the "forward phase," where GES greedily **adds edges** (connections) to the causal map, always choosing the single edge that most improves the model's score, until no more additions can improve it.

---

### **\#\# Phase 2: Backward Search (Refining with a Chisel)**

Now, the sculptor puts down the clay and picks up her chisel. Looking at her detailed rough draft, she begins to refine it. She considers all possible pieces she could remove. At each step, she makes the **single best chisel** that removes an unnecessary detail and most improves the statue's lifelike quality.

She continues this "greedy" removal process until any further chiseling would harm the statue.

* **In algorithm terms**: This is the "backward phase," where GES takes the complex map from phase one and greedily **removes edges**, always choosing the single edge whose removal most improves the score.

---

### **\#\# Why This Two-Phase Approach is Better**

By first building up a model and then pruning it down, GES can explore the possible causal structures much more efficiently and is less likely to get stuck on a simple "hill" than the standard Hill Climb algorithm. It has a better chance of finding the true "mountain" (the globally optimal causal map).

	