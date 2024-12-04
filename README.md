# Exercise 7: Demonstrate That the Sharing of Resources in a Multitasking Design Can Lead to a Deterioration in System Performance

---

## **Introduction**

In multitasking systems, resource sharing is often unavoidable. While shared resources can optimize hardware utilization, they also introduce challenges like contention, increased latency, and unpredictable task execution. This exercise critically examines how such resource-sharing impacts system performance using a real-time embedded system setup. Specifically, the experiment employs tasks controlling LEDs to visualize the effect of resource contention on task timing.

This demonstration encourages analytical thinking about:
1. **Design trade-offs**: Why resource sharing, though necessary, can compromise real-time performance.
2. **System dynamics**: How the interactions between tasks affect overall system behavior.
3. **Preventive measures**: Strategies to mitigate performance deterioration in multitasking systems.

---

## **Hypothesis**

Resource sharing among tasks will lead to observable delays in task execution, manifesting as irregular LED flashing patterns. Higher-priority tasks may worsen delays for lower-priority tasks, and without proper synchronization, the system risks becoming unresponsive.

---

## **Experiment Setup**

### **System Configuration**

#### **Tasks**
- **GreenLEDTask**:
  - Flashes the green LED at 200 ms intervals.
  - Interacts with the shared resource (`AccessSharedData`).

- **RedLEDTask**:
  - Flashes the red LED at 550 ms intervals.
  - Also interacts with the shared resource.

- **OrangeLEDTask**:
  - Toggles the orange LED every 50 ms.
  - Does not interact with the shared resource.

#### **Shared Resource**
The shared resource is a critical section simulated by `AccessSharedData`. It deliberately includes a long access delay using `TIM2`, designed to:
1. Magnify resource contention effects for easier observation.
2. Model real-world scenarios like accessing hardware peripherals or shared memory.

---

## **Code Analysis**

### 1. **Critical Section Management**
Critical sections ensure exclusive access to shared resources:
```c
taskENTER_CRITICAL();
AccessSharedData();
taskEXIT_CRITICAL();
```
**Analysis**:
- The use of critical sections effectively prevents race conditions but blocks other tasks, increasing latency.
- The effect is more pronounced when multiple tasks compete for the same resource, leading to task delays.

### 2. **AccessSharedData Function**
This function represents the shared resource and introduces a simulated delay:
```c
while (TIM2->CNT < 1000000) { }
```
**Analysis**:
- This delay exacerbates contention, making the effects visible in the LED flashing patterns.
- In real-world systems, such delays could stem from bottlenecks in accessing shared hardware or memory.

### 3. **Task Priorities**
The system assigns task priorities as follows:
- `GreenLEDTask`: Normal priority.
- `RedLEDTask`: Normal priority.
- `OrangeLEDTask`: Above-normal priority.

**Analysis**:
- The higher priority of `OrangeLEDTask` ensures consistent toggling of its LED.
- Lower-priority tasks (`GreenLEDTask` and `RedLEDTask`) experience delays due to resource contention and priority inversion, where a higher-priority task indirectly delays a lower-priority task through resource locking.

---

## **Observations**

### 1. **With Independent Tasks**
When tasks operate without resource sharing:
- LEDs flash at their expected intervals.
- The system exhibits predictable behavior.

### 2. **With Shared Resources**
When tasks share `AccessSharedData`:
1. **Delays**:
   - `GreenLEDTask` and `RedLEDTask` experience irregular flashing intervals due to contention.
   - Tasks are forced to wait, leading to cumulative delays.

2. **Impact on Task Timing**:
   - Higher-priority tasks preempt lower-priority tasks, compounding delays for the latter.
   - This dynamic could lead to deadline misses in real-time systems.

3. **OrangeLEDTask Behavior**:
   - Its consistent toggling highlights how unaffected tasks operate independently of shared resource contention.

**Critical Insight**: The degree of degradation correlates directly with the frequency and duration of shared resource access. In this experiment, the deliberate long access time amplifies this effect.

---

## **Discussion and Analysis**

### **1. Real-Time System Challenges**
- **Latency**: As observed, contention increases latency for lower-priority tasks, reducing overall system responsiveness.
- **Priority Inversion**: Although not severe in this example, priority inversion could occur if a lower-priority task holds a resource needed by a higher-priority task.
- **Scalability Issues**: As the number of tasks or shared resources increases, the system's behavior could degrade exponentially.

### **2. Implications of Resource Sharing**
This experiment reflects real-world scenarios like accessing shared memory or peripherals. It underscores the importance of:
- Minimizing shared resource access.
- Optimizing critical section durations to balance task responsiveness.

**Key Insight**: Proactive design choices can prevent resource-sharing issues but often come with trade-offs in system complexity or hardware cost.

---

## **Conclusion**

This demonstration highlights the criticality of considering resource contention in multitasking systems. While synchronization mechanisms like critical sections ensure safety, they can also impede system performance if not designed carefully. By simulating these effects using LEDs, this experiment provides a tangible understanding of how resource sharing affects real-time systems.

**Final Thought**: Resource sharing, though powerful, must be meticulously managed to maintain a balance between system safety and performance. This experiment provides a stepping stone for further exploration of advanced synchronization methods like priority inheritance protocols and hardware-based solutions.

---

## **Author**
- Dana Yoga Setya Ikhwandi (3222600016)
- Nio Perdana Azizudin (3222600022)
