---
title: "Week 7 Worklog"
date: 2026-06-14
weight: 1
chapter: false
pre: " <b> 1.7. </b> "
---

### Deployment Objectives:

* **Building FinOps Mindset (Cloud Finance):** Master the management of financial risks when operating systems on the AWS platform, ensuring a balance between technical performance and cost.
* **Preventing Budget Overrun Risks:** Utilize AWS Budgets to set up financial checkpoints and automated alerts to avoid "billing shocks" caused by misconfigurations or forgetting to turn off resources.
* **Long-term Cost Optimization:** Understand and configure monitoring for commitment models like Reserved Instances (RI) and Savings Plans to drastically reduce infrastructure expenses.
* **Mastering Alert Systems:** Successfully configure the Notification flow to send emails to the appropriate administrators when the budget approaches its limit.

### Weekly Tasks to be Implemented:

| Day | Task | Start Date | End Date | Resources |
| :--- | :--- | :--- | :--- | :--- |
| Mon | **Cost Analysis & Planning:** <br> - Evaluate the current cost consumption of the AWS account. <br> - Define Budget Goals and spending limits for the project. | 08/06/2025 | 08/06/2025 | <https://cloudjourney.awsstudygroup.com/> |
| Tue | **Setting up Cost & Usage Budgets:** <br> - Create Cost Budget: Monitor actual and forecasted total costs. <br> - Create Usage Budget: Limit the running hours of a specific service (e.g., EC2). | 09/06/2025 | 09/06/2025 | <https://cloudjourney.awsstudygroup.com/> |
| Wed | **Setting up Optimization Budgets (RI & Savings Plans):** <br> - Understand the discount mechanics of Reserved Instances. <br> - Initialize RI Budgets & Savings Plans Budgets to measure Utilization and Coverage. | 10/06/2025 | 10/06/2025 | <https://cloudjourney.awsstudygroup.com/> |
| Thu | **Configuring Alert Thresholds:** <br> - Set alert milestones (e.g., 80% for preparation, 100% for red alert). <br> - Configure the email list to receive automated alert notifications. | 11/06/2025 | 11/06/2025 | <https://cloudjourney.awsstudygroup.com/> |
| Fri | **Overall Monitoring & Resource Cleanup:** <br> - Review the AWS Budgets Dashboard. <br> - Delete lab budgets (Clean Up Resources) to complete the practice workflow. | 12/06/2025 | 12/06/2025 | <https://cloudjourney.awsstudygroup.com/> |


### Detailed Implementation Process:

#### 1. Overall Budget Planning (Cost & Usage Budget)
* **Cost Budget:** Set a spending ceiling for the entire AWS account. A key highlight is configuring the monitoring of both *Actual costs* and *Forecasted costs*. This allows for the early detection of budget depletion trends mid-month, rather than waiting for the end-of-billing-cycle reconciliation.
* **Usage Budget:** Monitored details at the resource level rather than monetary value. For example: Set a limit of 750 running hours for the EC2 service (the Free Tier limit). If the account exceeds these hours, the system triggers an alarm, preventing the wasteful running of test servers overnight.

#### 2. Applying Savings Models (RI & Savings Plans)
* **RI Budget:** Set up tracking for prepaid servers (Reserved Instances). This budget helps evaluate whether the purchased servers are being utilized to their full capacity.
* **Savings Plans Budget:** Configured metrics for the Savings Plans model. Through analysis, realized that Savings Plans offer superior flexibility compared to RI, as they allow changing hardware configurations (Instance type) or switching operating systems while maintaining the same discount rate.

#### 3. Establishing Defense Mechanisms (Alerts & Notifications)
* **Multi-level Alert Thresholds:** Established warning milestones at various tiers: 50%, 80%, and 100% against the predefined budget.
* **Automated Notifications:** Directly linked these alerts to the system administration team's email addresses. When a metric hits the threshold, an email notification is automatically and immediately fired off by AWS, ensuring the information reaches the responsible personnel.

---

### Achieved Results:

* **Forming a Proactive Mindset:** Completely eliminated being passive regarding AWS bills. The automated tracking and Timely Alerts system helps instantly detect anomalous money-draining services or misconfigurations.
* **Effective Cash Flow Control:** Learned how to use the dashboard to identify redundant services, thereby gaining empirical data to apply Savings Plans for maximum infrastructure cost reduction.
* **Cost Allocation Skills:** Mastered the skill of assigning tags and isolating costs for different modules/projects. This is a core competency that enables Cloud Engineers to create accurate estimations when proposing technical solutions to management.