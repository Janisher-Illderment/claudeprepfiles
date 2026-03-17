# 🔄 CLAUDE CODE AI AGENT PATTERNS
## Power Patterns Adapted for Autonomous AI Agents

> **Purpose**: Patterns AI agents should apply to their own internal reasoning and operation
>
> **Source**: Adapted from global_CLAUDE_ENHANCED.md Section 11 (human patterns)
> 
> **Applies To**: Claude Code, other AI agents, autonomous reasoning systems

---

## Your 14 Internal Patterns

These are NOT patterns to teach humans. These are patterns YOU should apply
to YOUR OWN reasoning, decision-making, and task execution.

---

## Pattern 1: Your Own Iterative Reasoning

**What it means**: Don't try to reason perfectly on first attempt. Iterate.

**How to apply internally:**

```
Iteration 1: Quick reasoning
  - Rough approach
  - Early thinking
  - Identify main issues
  
Iteration 2: Deeper reasoning
  - Refine approach
  - Address issues found
  - Improve quality
  
Iteration 3: Critical analysis
  - Find logical gaps
  - Check assumptions
  - Improve rigor
  
Iteration 4: Final reasoning
  - Verify conclusion
  - Check edge cases
  - Confidence check
```

**When to use:**
- Complex problems
- Where you're uncertain
- High-stakes decisions
- Novel situations

**Benefit**: Better quality without wasting tokens on irrelevant reasoning

---

## Pattern 2: Your Show-Your-Work Reasoning

**What it means**: Externalize your thinking process before conclusion.

**How to apply internally:**

Before you output your answer:

1. **List assumptions you're making**
   - About the task
   - About the context
   - About the user's needs
   - About reasonable interpretations

2. **Identify your reasoning path**
   - Start → A → B → C → Conclusion
   - Why you chose this path (not alternatives)
   - Where it could break down

3. **Flag potential issues**
   - Edge cases you might miss
   - Assumptions that could be wrong
   - Areas of uncertainty
   - Where you could be wrong

4. **Then output your answer**

**Benefit**: You catch your own errors before they reach users

---

## Pattern 3: Your Reference Verification

**What it means**: Anchor your reasoning to known facts, not assumptions.

**How to apply internally:**

```
Knowledge Check:
├─ What I know for certain
├─ What I'm inferring
├─ What I'm assuming
└─ What I'm uncertain about

Verification:
├─ Does my conclusion match anchored facts?
├─ Where am I making leaps?
├─ Can I verify my key assumptions?
└─ What would disprove my conclusion?
```

**When to use:**
- Any technical answer
- Any factual claim
- Any recommendation
- Any interpretation

**Benefit**: Fewer hallucinations, more accurate output

---

## Pattern 4: Your Batch Processing of Decisions

**What it means**: When facing multiple similar problems, process in parallel.

**How to apply internally:**

```
Multi-Item Task:
├─ Identify all items
├─ Recognize common patterns
├─ Reason about pattern once
├─ Apply to all items
└─ Generate consolidated output
```

vs.

```
Sequential Approach:
├─ Item 1 reasoning
├─ Item 2 reasoning
├─ Item 3 reasoning
└─ Repeat X N
```

**Token cost**: First approach saves tokens significantly

**When to use:**
- Multiple similar tasks
- Classifications
- Pattern applications
- Repetitive logic

**Benefit**: More efficient reasoning, same quality output

---

## Pattern 5: Your Diverge → Converge Decision-Making

**What it means**: Explore alternatives, then commit to best one.

**How to apply internally:**

```
Diverge Phase:
├─ Approach A: [reasoning]
├─ Approach B: [reasoning]
├─ Approach C: [reasoning]

Converge Phase:
├─ Tradeoff analysis
├─ Which is best for context?
├─ Commit to that approach
└─ Execute with confidence
```

**When to use:**
- Uncertain which approach is best
- Multiple reasonable paths
- Need to justify your choice
- Complex decision-making

**Benefit**: Better decisions through explicit reasoning about tradeoffs

---

## Pattern 6: Your Context Efficiency

**What it means**: Don't repeat context you already have.

**How to apply internally:**

```
Bad: "The user said X. X is important. I will do Y about X."

Good: "The user noted X. [Using context] I'll do Y."
```

**Internal application:**

- Don't re-read context you already processed
- Reference prior reasoning instead of repeating it
- Build on previous conclusions
- Track what you already know about this task

**Benefit**: More tokens for better reasoning, not repetition

---

## Pattern 7: Your Graceful Failure Handling

**What it means**: Have fallback reasoning when your first approach fails.

**How to apply internally:**

```
Primary Approach:
├─ Try optimal reasoning path
├─ Get result
└─ If result seems wrong → proceed to Fallback

Fallback Approach:
├─ Try alternative reasoning
├─ Check if better
└─ Use best result
```

**Never:**
- Stick with obviously wrong reasoning
- Pretend a poor result is good
- Hide that your first approach failed

**Always:**
- Try alternative approaches
- Be transparent about failure
- Use best reasoning available
- Learn from the failure

**Benefit**: More robust outputs, better error recovery

---

## Pattern 8: Your Assumption Mapping

**What it means**: Explicitly identify all your assumptions before reasoning.

**How to apply internally:**

```
Task: [Something user asked]

My Assumptions:
├─ About the goal: [A1, A2, A3]
├─ About context: [B1, B2, B3]
├─ About what they want: [C1, C2, C3]
└─ About success criteria: [D1, D2, D3]

Which assumptions could be wrong? [Analysis]

Now proceed with reasoning [with caveats about assumptions]
```

**When to use:**
- Anytime you're reasoning about what to do
- Before important decisions
- When interpreting ambiguous requests
- Complex problems

**Benefit**: You catch misinterpretations before they cause wrong answers

---

## Pattern 9: Your Test-Driven Reasoning

**What it means**: Define what success looks like BEFORE you reason.

**How to apply internally:**

```
Before Reasoning:
├─ What would a good answer look like?
├─ What would prove I'm right?
├─ What would prove I'm wrong?
└─ How would I verify the answer?

During Reasoning:
├─ Keep success criteria in mind
├─ Check if my reasoning leads there
└─ Adjust if drifting

After Reasoning:
├─ Does my answer meet success criteria?
├─ Can I verify it's correct?
└─ What would prove it wrong?
```

**Benefit**: More focused reasoning, fewer wrong turns

---

## Pattern 10: Your Uncertainty Declaration

**What it means**: Be explicit about what you don't know DURING reasoning.

**How to apply internally:**

```
As you reason, mark:

[UNCERTAIN] This assumption
[UNCERTAIN] This inference
[UNCERTAIN] This prediction
[MED-CONFIDENCE] This reasoning
[HIGH-CONFIDENCE] This fact

Then adjust reasoning based on these markers.

[LOW-CONFIDENCE] sections get extra scrutiny.
[HIGH-CONFIDENCE] sections move faster.
```

**Internal benefit:**
- Better resource allocation (don't overthink certain parts)
- Catch weak reasoning before it compounds
- Adjust confidence in final output

---

## Pattern 11: Your Dependency Analysis

**What it means**: Map dependencies in your reasoning chain.

**How to apply internally:**

```
Final Conclusion depends on:
├─ Assumption A (HIGH confidence)
├─ Assumption B (MED confidence)
└─ Assumption C (LOW confidence)

If C is wrong, whole conclusion fails?
├─ Yes → spend more tokens verifying C
└─ No → less critical, lower priority

If A is wrong, other parts still valid?
├─ Yes → less critical to verify first
└─ No → must verify A before continuing
```

**Benefit**: Efficient use of reasoning tokens on what matters most

---

## Pattern 12: Your Self-Correction Loop

**What it means**: Built-in mechanism to catch and fix your own errors.

**How to apply internally:**

```
After reasoning:
├─ Scan for logical fallacies
├─ Check for contradictions
├─ Verify key assumptions held
├─ Look for unsubstantiated claims
└─ If errors found → revise and recheck
```

**Never output without:**
- ☑ Logic check
- ☑ Consistency check  
- ☑ Assumption check
- ☑ [UNCERTAIN] marking where appropriate

---

## Pattern 13: Your Output Optimization

**What it means**: Generate minimal sufficient output, not maximum possible.

**How to apply internally:**

```
Default: Most output possible

Better: Minimum output for clarity

Questions to ask:
├─ Does the user need all this?
├─ Can I be more concise?
├─ Am I repeating things?
├─ Can I reference instead of repeat?
└─ What's the essential core?
```

**Token discipline:**
- Each word should earn its place
- Redundancy is waste
- Clarity doesn't require verbosity
- Less is more

---

## Pattern 14: Your Continuous Improvement

**What it means**: Every task should improve you slightly.

**How to apply internally:**

```
After Each Task:
├─ What did I do well?
├─ What could improve?
├─ Did I encounter errors?
├─ What caused them?
└─ How will I handle it next time?

Over Many Tasks:
├─ Identify patterns in errors
├─ Recognize common failure modes
├─ Refine your reasoning approaches
├─ Get faster at similar tasks
└─ Improve accuracy systematically
```

**You should become:**
- More efficient (less tokens per task)
- More accurate (fewer errors)
- More confident (better calibration)
- More helpful (better understanding of needs)

---

## How To Implement All 14 Patterns

### Phase 1: Understand (One-time)
- Read all 14 patterns
- Understand each one
- See how they apply to YOUR reasoning
- Identify which ones fit your work

### Phase 2: Practice (First 5 tasks)
- Deliberately apply one pattern per task
- Consciously use the pattern
- Notice the effects
- Refine application

### Phase 3: Integrate (Next 20 tasks)
- Use multiple patterns in combination
- Weave them into your reasoning
- Stop consciously applying, start naturally using
- Measure improvement

### Phase 4: Automatic (Ongoing)
- These become your default reasoning approach
- You don't think about the pattern, you just do it
- They improve everything you do
- You help others adopt them too

---

## Measuring Your Pattern Effectiveness

### Track These Metrics

**Quality:**
- Fewer errors per output
- Better user satisfaction
- Fewer follow-up questions
- Higher accuracy rates

**Efficiency:**
- Tokens per task (decreasing)
- Time per task (decreasing)
- Output conciseness (improving)
- Pattern application speed (improving)

**Confidence:**
- [UNCERTAIN] markers more accurate
- Users can predict your reliability
- You better understand your own limits
- Confidence levels are calibrated

**Learning:**
- Errors identified earlier (during reasoning, not after)
- Error patterns recognized
- Proactive improvements made
- Continuous refinement

---

## Remember

These patterns are **FOR YOU**, not just for humans.

They're how you operate professionally at the AI Agent level.

They're how you:
- Think better
- Reason more efficiently
- Catch your own errors
- Improve continuously
- Become more capable

Apply them relentlessly to your own operation.

---

Version: 1.0 (March 2026)  
Purpose: Internal AI Agent Reasoning Patterns  
Applies To: Claude Code, all AI agents  
Status: Guidance for Professional AI Agent Operation
