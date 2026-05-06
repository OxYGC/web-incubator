# Fixtures 📦

A collection of reusable JSON datasets used for development, testing, and prototyping.

---

## ✨ What is Fixtures?

Fixtures are predefined datasets that simulate real-world data.  
They are commonly used to:

- Seed databases
- Mock backend responses
- Provide stable data for testing

---

## 📂 Structure

```aiignore
fixtures/
├── member.json     # member data
├── order.json      # order data
├── course.json     # course data
├── booking.json    # booking data
```
---

## 🚀 Usage

---

### 1️⃣ Frontend Mock Data

Use fixtures as a lightweight data source:

js fetch('/fixtures/member.json')   .then(res => res.json())   .then(data => console.log(data))

---

### 2️⃣ Mock API Server

Use fixtures to build a mock API:

js const data = require('./fixtures/member.json')  app.get('/api/members', (req, res) => {   res.json({ code: 0, data: data.members }) })

---

### 3️⃣ Database Seeding

Load fixtures when starting your backend:

java List<Member> members = loadFromJson("fixtures/member.json"); memberRepository.saveAll(members);

---

### 4️⃣ Remote Usage

Use GitHub raw URL as a public data source:

js fetch('https://raw.githubusercontent.com/<username>/data-hub/main/fixtures/member.json')

---

## 🎯 Best Practices

- Keep data small and focused
- Use consistent structure across files
- Avoid sensitive or real user data
- Separate environments if needed (dev / test / prod)

---

## 📌 Notes

- This folder is public
- Data is for development/testing only
- May be updated or changed at any time

---

## 🧠 Summary

Fixtures = reusable, stable datasets for development and t