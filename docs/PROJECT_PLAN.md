# 🧠 Personal Blog Project Plan – Joe Gonwa

---

## ✅ **Purpose & Motivation**

The blog is a personal digital space to:
- Think in public, reflect, and explore writing as a habit
- Track and share side projects, ideas, and reading notes
- Build an intentional, long-lived online presence under your own domain
- Create a clear and minimalist destination that reflects your intellectual and creative voice—**not performative, not creator-focused**

---

## 🎯 **Personal Goals & Milestones**

### **Pre-Launch (Complete)**
- [x] Choose platform: **Hugo + PaperMod**
- [x] Create clean folder structure + site skeleton
- [x] Connect repo to GitHub + Netlify
- [x] Custom domain (`joegonwa.com`)
- [x] Clean out legacy theme content and submodules

### **Launch**
- [ ] Publish live version with:
  - About page
  - At least 1 essay in “Writing”
  - Placeholder pages for Reading, Projects, Now
- [ ] Add favicon, metadata, RSS feed
- [ ] Public deploy to Netlify under domain

### **Post-Launch / Ongoing**
- [ ] Publish 1 new post (essay or margin) every 2–4 weeks
- [ ] Keep “Now” page updated quarterly
- [ ] Log books into Reading shelf monthly
- [ ] Log 1 new Project every 1–2 months

---

## 🧱 **Platform & Stack**

- **Static site generator**: [Hugo](https://gohugo.io)
- **Theme**: [PaperMod](https://github.com/adityatelange/hugo-PaperMod)
- **Hosting**: [Netlify](https://netlify.com)
- **Domain**: `joegonwa.com` (connected via Netlify)
- **Analytics** (optional post-launch): [Umami](https://umami.is)

---

## 🗂️ **Information Architecture (IA)**

```
/
├── Home      → Intro + latest posts
├── About     → Bio, values, blog purpose
├── Writing   → Polished essays and reflections
├── Margins   → Quote + commentary posts
├── Projects  → Logs of active experiments + tools
├── Reading   → Book notes, ratings, reflections
├── Now       → Personal “current focus” log
└── Contact   → Email or social links
```

---

## 🧰 **Logistics – How to Build**

### Local Development
```bash
hugo server -D
```

### GitHub Deployment
- Committed to `main` branch
- Netlify auto-builds on push
- Build command: `hugo --gc --minify`
- Publish directory: `public/`

---

## 🗓️ **Content Plan**

### **What to Post**
- **Essays**: Deep reflections on attention, tech, work, meaning
- **Margins**: Short takes on quotes, books, interviews, ideas
- **Projects**: Real-time logs or updates on side projects
- **Reading**: Book reactions, 1–2 paragraph reviews or notes
- **Now**: Snapshot of current routines, focus, curiosities

### **When to Post**
- Essays or notes: Every 2–4 weeks
- Margins: As often as you highlight something worth sharing
- Projects: New post per project or milestone
- Reading log: Monthly book log updates
- Now page: Updated quarterly

### **Where to Post**
- All content lives on your blog (`joegonwa.com`)
- Optional: Share select essays to Twitter or LinkedIn with short summary (post-launch)