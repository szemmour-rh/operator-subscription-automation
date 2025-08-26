# OpenShift Operator Subscription Approval Automation

This project automates the switching of Operator `Subscription` approval modes (`Automatic` ↔ `Manual`) in an OpenShift cluster based on working hours.

## 🎯 Purpose

In OpenShift, Operators installed via OLM (Operator Lifecycle Manager) can be configured to upgrade automatically or require manual approval. This automation provides fine-grained control by adjusting upgrade behavior based on time:

- 🕘 **Working Hours (Weekdays 09:00–18:00):**  
  Operator upgrades require **manual approval** to reduce risks during active user hours.

- 🌙 **After Hours & Weekends (Evenings & Sat/Sun):**  
  Operator upgrades are set to **automatic**, allowing seamless background updates.

---

## 🚀 What It Does

- Deploys two CronJobs in a dedicated namespace:
  - `set-subscriptions-manual` — enforces `Manual` mode during working hours
  - `set-subscriptions-automatic` — sets `Automatic` mode after hours and on weekends
- Uses a dedicated **ServiceAccount** with minimal `RBAC` to patch `Subscription` resources cluster-wide
- Based on the `ose-cli` container image for access to `oc`

---

## 📁 Project Structure

├── namespace.yaml                    # Namespace for automation
├── rbac.yaml                         # ServiceAccount, ClusterRole, and Binding
├── patch-subscriptions-manual.yaml   # CronJob: Manual mode during weekdays
├── patch-subscriptions-automatic.yaml # CronJob: Automatic mode after hours/weekends



## 🔐 Security

- Uses a **least-privilege RBAC policy**
- Only allowed to:
  - `get`, `list`, and `patch` Subscriptions (`operators.coreos.com`)
- Scoped to a dedicated namespace for isolation

---

## 🛠 Requirements

- OpenShift 4.x
- Access to Red Hat's `ose-cli` image (or alternative CLI image)
- Cluster-admin access (one-time setup to apply RBAC and CronJobs)

---

## 📦 Installation

Apply all resources using:

```bash
oc apply -f namespace.yaml
oc apply -f rbac.yaml
oc apply -f patch-subscriptions-manual.yaml
oc apply -f patch-subscriptions-automatic.yaml
