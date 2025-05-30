# mannyai_technical_assesment
# Forward Planning Apparel Scheduling

This project simulates a simplified apparel production process with three sequential stages: **Cut → Sew → Pack**. The simulation considers multiple constraints such as machine availability, product-specific setup times, and additional delays. A Monte Carlo simulation is used to explore various scheduling permutations with the aim of minimizing overall lateness and maximizing on-time order completions.

---

## My Approach and Steps

### Step 1 & 2: Prepare the Data and Machines
- Loaded the order and machine data.
- Structured the data for easy processing during simulation.

### Step 3: Implement Forward Planning Logic
- Modeled the manufacturing flow: **Cut → Sew → Pack**.
- Incorporated machine setup times, delays, and dependencies between stages.

### Step 4: Generate Order Permutations and Run Monte Carlo Simulations
- Created multiple order sequences using heuristic methods.
- Applied Monte Carlo simulations to evaluate different scheduling outcomes.

### Step 5: Evaluate and Select Best Schedule
- Compared simulation results based on:
  - Number of on-time orders.
  - Average lateness.
- Selected the schedule with the best trade-off.

### Step 6: Report Results
- Summarized outcomes:
  - Total orders completed on time.
  - Average units of lateness.
  - Detailed lateness by order.
- Created basic visualizations for better interpretation.

### Extra: Interactive Gradio App
- Wrapped everything into a simple **Gradio UI**.
- Allows for input tweaking and viewing simulation results interactively.

---

## Dashboard

![Gradio version dashboard](https://raw.githubusercontent.com/Puplegeek/mannyai/master/diagrams/gradio%20version%20dasboard.png)

[Access the live Gradio demo here](https://huggingface.co/spaces/syedakash/Monte-Carlo-Scheduling)

---

## Results

i. Total orders completed on time: 20/50
ii. Average time units late: 107.72
iii. Summary of lateness by order:

| **Order** | **Units Late** |
|-----------|----------------|
| O001      | 145            |
| O002      | 0              |
| O003      | 122            |
| O004      | 273            |
| O005      | 0              |
| O006      | 85             |
| O007      | 326            |
| O008      | 0              |
| O009      | 0              |
| O010      | 78             |
| O011      | 65             |
| O012      | 0              |
| O013      | 41             |
| O014      | 0              |
| O015      | 341            |
| O016      | 0              |
| O017      | 430            |
| O018      | 126            |
| O019      | 410            |
| O020      | 0              |
| O021      | 0              |
| O022      | 0              |
| O023      | 0              |
| O024      | 60             |
| O025      | 162            |
| O026      | 0              |
| O027      | 224            |
| O028      | 36             |
| O029      | 168            |
| O030      | 261            |
| O031      | 0              |
| O032      | 0              |
| O033      | 171            |
| O034      | 349            |
| O035      | 104            |
| O036      | 220            |
| O037      | 62             |
| O038      | 153            |
| O039      | 130            |
| O040      | 0              |
| O041      | 0              |
| O042      | 0              |
| O043      | 0              |
| O044      | 0              |
| O045      | 162            |
| O046      | 196            |
| O047      | 237            |
| O048      | 138            |
| O049      | 0              |
| O050      | 111            |


## Table of Contents

1. [Overview](#overview)
2. [Project Structure](#project-structure)
3. [Step-by-Step Explanation](#step-by-step-explanation)
    - [Data Loading](#data-loading)
    - [Class Definitions](#class-definitions)
    - [Scheduling Core](#scheduling-core)
    - [Permutation Generator](#permutation-generator)
    - [Optimization Engine](#optimization-engine)
    - [Visualization Functions](#visualization-functions)
    - [Gradio Interface](#gradio-ui)
4. [Detailed Tables for Functions and Variables](#detailed-tables-for-functions-and-variables)


---

## Overview

The simulation models the following production process:

1. **Cutting Stage:**  
   - Uses 2 cutting machines.
   - Incurs a 10-time-unit setup time if the product type changes.
2. **Sewing Stage:**  
   - Uses 3 sewing machines.
   - For some orders, a post-cutting delay of 48 time units is required.
3. **Packing Stage:**  
   - Uses 1 packing station.

Each order, represented by its product type, processing times, deadline, and delay flag, is scheduled through these stages. The simulation uses a forward-planning scheduler to assign orders to the first available machine and then runs a Monte Carlo simulation (with 100–500 iterations) to find the best schedule in terms of on-time completions and minimal lateness.

---

## Project Structure

- **Main Notebook (`version_01.ipynb`):**
  - **Data Loading:** Reads CSV data and converts required columns.
  - **Class Definitions:**  
    - `Order` for storing order attributes.
    - `Machine` for tracking machine availability and utilization.
  - **Scheduling Core:**  
    - `simulate_schedule(orders)` simulates the production process.
  - **Permutation Generator:**  
    - `generate_permutation(orders)` creates new order sequences using heuristics.
  - **Optimization Engine:**  
    - `optimize_schedules(data, iterations)` runs the Monte Carlo simulation.
  - **Visualization Functions:**  
    - Functions to generate timeline, progress, lateness distribution, and heuristic breakdown charts.

---

## Detailed Tables for Functions and Variables

The tables below provide a clear mapping between the code structure and its functionality. They detail the variables/parameters used in each function or class and describe the underlying logic and purpose.

---

## Step-by-Step Explanation

### Data Loading

**Function: `load_data(csv_path)`**

- **Purpose:** Reads the CSV file containing order data and converts the delay flag to boolean.
  
**Table:**

| **Variable/Parameter** | **Description**                                          | **Logic/Purpose**                                                        |
|------------------------|----------------------------------------------------------|--------------------------------------------------------------------------|
| `csv_path`             | Path to the CSV file                                     | Specifies where to load the order data from.                           |
| `df`                   | DataFrame loaded using `pd.read_csv(csv_path)`           | Contains order data; the delay flag is converted from text to Boolean.   |

---

### Class Definitions

#### Order Class

**Purpose:** Encapsulates the attributes of each order.

**Table:**

| **Attribute**         | **Source (CSV Column)**           | **Description**                                    | **Logic/Purpose**                                                    |
|-----------------------|-----------------------------------|----------------------------------------------------|----------------------------------------------------------------------|
| `order_id`            | `order_id`                        | Unique identifier for each order.                | Used to track and reference orders.                                  |
| `product_type`        | `Product type`                    | Type of product for the order.                   | Determines if a setup time is needed on cutting machines.            |
| `cut_time`            | `cut time`                        | Time units required for cutting.                 | Used in scheduling the cutting stage.                                |
| `sew_time`            | `sew time`                        | Time units required for sewing.                  | Used in scheduling the sewing stage.                                 |
| `pack_time`           | `pack time`                       | Time units required for packing.                 | Used in scheduling the packing stage.                                |
| `deadline`            | `deadline`                        | Order delivery deadline (in time units).         | Used to compute lateness.                                              |
| `requires_delay`      | `requires_out_of_factory_delay`   | Boolean indicating if a post-cut delay is needed.  | Determines whether to add a 48-unit delay after cutting.             |

#### Machine Class

**Purpose:** Models machines for each production stage, tracking their availability and utilization.

**Table:**

| **Attribute**    | **Default/Initialization**           | **Description**                                        | **Logic/Purpose**                                                       |
|------------------|----------------------------------------|--------------------------------------------------------|-------------------------------------------------------------------------|
| `machine_id`     | Combination of type and ID (e.g., `Cut-1`) | Identifier for each machine.                          | Distinguishes machines and tracks order assignment.                   |
| `available_at`   | `0` (initially)                        | Next available time of the machine.                   | Updated after each task to schedule subsequent orders.                 |
| `last_product`   | `None` (initially)                     | Last product type processed by the machine.           | Determines if setup time is incurred when switching product types.     |
| `utilization`    | `[]` (empty list)                      | List of tuples with start and end times for tasks.    | Used later for visualizing machine usage in a timeline (Gantt chart).     |

---

### Scheduling Core

**Function: `simulate_schedule(orders)`**

- **Purpose:** Schedules each order through the Cut → Sew → Pack stages and computes lateness.
- **Process:**
  1. **Cutting Stage:**  
     - Evaluate each cutting machine.
     - Apply a 10-unit setup time if the product type changes.
     - Choose the machine with the earliest available start time.
  2. **Sewing Stage:**  
     - Adjust start time for a potential 48-unit delay.
     - Choose the earliest available sewing machine.
  3. **Packing Stage:**  
     - Schedule on the packing machine based on availability.
  4. **Lateness Calculation:**  
     - Compare finishing time with the deadline and compute lateness.

**Table:**

| **Variable/Parameter** | **Description**                                               | **Logic/Purpose**                                                                 |
|--------------------------|---------------------------------------------------------------|-----------------------------------------------------------------------------------|
| `orders`                 | List of `Order` objects                                       | The set of orders to be scheduled.                                                |
| `cutting_machines`       | List of 2 `Machine` objects                                   | Simulates the two cutting tables.                                                 |
| `sewing_machines`        | List of 3 `Machine` objects                                   | Simulates the three sewing stations.                                              |
| `packing_machine`        | Single `Machine` object                                       | Simulates the packing station.                                                    |
| `schedule`               | Dictionary mapping order IDs to schedule details              | Stores assignment and timing details for each order across stages.                |
| `lateness_summary`       | Dictionary mapping order IDs to lateness values               | Tracks lateness per order.                                                        |
| `total_lateness`         | Accumulated lateness for all orders                           | Provides an overall measure of schedule performance.                            |
| `on_time_count`          | Count of orders with zero lateness                            | Used to evaluate on-time performance.                                             |
| `cut_options`            | List of tuples (`start_time`, machine) for cutting stage      | Considers each machine’s availability and setup time to choose the earliest start.  |
| `sew_options`            | List of tuples (`start_time`, machine) for sewing stage       | Determines the earliest start times for sewing, considering post-cut delay.       |
| `lateness`               | `max(0, end_pack - order.deadline)`                           | Computes how late an order is (if at all).                                        |

---

### Permutation Generator

**Function: `generate_permutation(orders)`**

- **Purpose:** Creates different order sequences using three heuristics to introduce variability in the simulation.
- **Heuristics:**
  - **Deadline:** 60% chance (sorts by deadline).
  - **Product Type:** 20% chance (groups by product type).
  - **Processing Time:** 20% chance (sorts by total processing time).

**Table:**

| **Variable/Parameter** | **Description**                                           | **Logic/Purpose**                                                                |
|--------------------------|-----------------------------------------------------------|----------------------------------------------------------------------------------|
| `orders`                 | List of `Order` objects                                   | The original order sequence to be permuted.                                      |
| `r`                      | Random float between 0 and 1                              | Determines which heuristic is used for this permutation.                       |
| `sorted_orders`          | Orders sorted by the selected key (deadline, product, time) | Organizes orders by the chosen heuristic.                                        |
| `groups`                 | Dictionary grouping orders by heuristic key               | Allows shuffling within groups to add variability.                               |
| `shuffled`               | Final list of permuted orders                             | New order sequence returned for simulation.                                    |
| `heuristic`              | String indicating the heuristic used                    | Useful for later analysis and tracking of heuristic performance.               |

---

### Optimization Engine

**Function: `optimize_schedules(data, iterations=500)`**

- **Purpose:** Uses Monte Carlo simulation to iterate through multiple order permutations to find the schedule that minimizes lateness while maximizing on-time completions.
- **Process:**
  1. Convert CSV data into `Order` objects.
  2. For a fixed number of iterations:
     - Generate a permutation using `generate_permutation`.
     - Simulate the schedule using `simulate_schedule`.
     - Track and update the best performing schedule.
  3. Record history for on-time counts, total lateness, and used heuristics.

**Table:**

| **Variable/Parameter** | **Description**                                                     | **Logic/Purpose**                                                               |
|--------------------------|---------------------------------------------------------------------|---------------------------------------------------------------------------------|
| `data`                   | DataFrame containing order information                             | Raw input data for simulation.                                                  |
| `iterations`             | Number of iterations to run (default: 500)                           | Controls how many permutations are tested.                                    |
| `orders`                 | List of `Order` objects (converted from the DataFrame)               | Represents all orders to be scheduled.                                          |
| `best`                   | Dictionary tracking the best schedule and performance metrics        | Stores the best schedule found during simulation.                             |
| `on_time_history`        | List recording on-time order counts for each iteration               | Used for visualizing progress.                                                  |
| `total_lateness_history` | List recording the total lateness for each iteration                   | Used for visualizing progress.                                                  |
| `heuristic_history`      | List recording the heuristic used in each iteration                    | Provides insight into the effectiveness of different heuristics.                |
| `permuted`               | Permuted list of orders (generated by `generate_permutation`)          | Current order sequence for simulation.                                        |
| `schedule`, `lateness_map`, `total_lat`, `on_time`, `cut_util`, `sew_util`, `pack_util` | Output values from `simulate_schedule`             | Metrics and schedule details for the current simulation iteration.              |

---

### Visualization Functions

These functions create visual representations of the simulation results:

#### plot_machine_timeline(schedule)

**Purpose:**  
Creates a Gantt chart showing the utilization timeline for each machine.

**Table:**

| **Variable/Parameter** | **Description**                                     | **Logic/Purpose**                                                                 |
|--------------------------|-----------------------------------------------------|-----------------------------------------------------------------------------------|
| `schedule`               | Dictionary with scheduling details per order        | Used to group tasks by machine and display start and duration times graphically.   |

**Logic Flow:**  
- Aggregate tasks by machine.
- Plot horizontal bars representing each task’s duration.
- Set axis labels and chart title.

---

#### plot_optimization_progress(on_time_history, lateness_history)

**Purpose:**  
Plots the evolution of on-time order counts and total lateness over simulation iterations.

**Table:**

| **Variable/Parameter** | **Description**                                  | **Logic/Purpose**                                                                  |
|--------------------------|--------------------------------------------------|------------------------------------------------------------------------------------|
| `on_time_history`        | List of on-time order counts per iteration        | Visualizes how on-time performance improves over iterations.                      |
| `lateness_history`       | List of total lateness values per iteration         | Shows the trend in total lateness over the simulation.                             |

**Logic Flow:**  
- Plot on-time counts on the primary y-axis.
- Plot total lateness on a secondary y-axis.
- Label axes and display the combined progress chart.

---

#### plot_lateness_distribution(lateness_map)

**Purpose:**  
Visualizes lateness for each order using a bar chart.

**Table:**

| **Variable/Parameter** | **Description**                                     | **Logic/Purpose**                                                                 |
|--------------------------|-----------------------------------------------------|-----------------------------------------------------------------------------------|
| `lateness_map`           | Dictionary mapping order IDs to their lateness values | Displays lateness per order to identify which orders are most delayed.             |

**Logic Flow:**  
- Sort lateness values.
- Plot a bar chart with order IDs on the x-axis and lateness on the y-axis.
- Add a reference line at zero to indicate on-time completion.

---

#### plot_heuristic_breakdown(heuristic_history)

**Purpose:**  
Generates a pie chart showing the distribution of heuristics used during the simulation.

**Table:**

| **Variable/Parameter** | **Description**                                 | **Logic/Purpose**                                                                |
|--------------------------|-------------------------------------------------|----------------------------------------------------------------------------------|
| `heuristic_history`      | List of heuristic names used in each iteration   | Counts occurrences to display which heuristics were most frequently applied.      |

**Logic Flow:**  
- Count the frequency of each heuristic.
- Plot a pie chart to display the proportional use of each heuristic.
- Display the breakdown chart.

---

## Gradio Interface Code Explanation

The following explains the Gradio interface code that creates an interactive web UI for running the production scheduling simulation:

- **`run_simulation` Function:**
  - **Purpose:**  
    Processes the uploaded CSV file and simulation parameters, runs the simulation, and prepares the results for display.
  - **Steps:**
    - **Seed Initialization:**  
      Calls `set_seed(42)` to ensure reproducible simulation results.
    - **Data Loading:**  
      Reads the CSV file (using `pd.read_csv(file.name)`) and processes it with `load_data`.
    - **Simulation Execution:**  
      Calls `optimize_schedules` with the loaded data and simulation parameters (number of iterations, and the number of cutting, sewing, and packing machines) to obtain the best schedule.
    - **Gantt Chart Generation:**  
      Generates an extra Gantt chart image via `create_machine_timeline_image` to visualize machine utilization.
    - **Result Formatting:**  
      Returns a tuple with:
        - The formatted count of on-time orders.
        - The formatted average lateness.
        - A lateness distribution chart.
        - An optimization progress chart.
        - A heuristic analysis chart.
        - The Gantt chart image.

- **Gradio Interface Setup (Using `gr.Blocks`):**
  - **Theme and Styling:**  
    The interface uses a soft theme (`gr.themes.Soft()`) and custom CSS to style numerical outputs.
  - **Layout:**
    - **Left Column (Configuration):**
      - Contains input elements:
        - Number inputs for setting the number of cutting tables, sewing machines, and packing stations.
        - A file upload widget to upload the orders CSV.
        - A slider to select the number of simulation iterations.
        - A dropdown to select the simulation algorithm (only "Monte Carlo" is available).
        - A button labeled "🚀 Start Simulation" to trigger the simulation.
    - **Right Column (Results):**
      - Displays output elements:
        - Markdown elements for showing the number of on-time orders and average lateness.
        - Tabs to display various charts:
          - A chart for lateness distribution.
          - A chart for optimization progress.
          - A chart for heuristic analysis.
          - An image (Gantt chart) showing machine utilization.
  - **Event Binding:**
    - The `run.click` method binds the "Start Simulation" button to the `run_simulation` function.
    - When the button is clicked, the inputs are passed to `run_simulation` and the outputs (results and charts) are displayed in the corresponding UI components.
  - **Launching the App:**
    - The app is launched with `app.launch(inline=True, share=True)`, which starts the Gradio server and provides an option to share a public link.

This code creates an interactive environment where users can upload their orders, configure simulation parameters, run the simulation, and view comprehensive visual results.



