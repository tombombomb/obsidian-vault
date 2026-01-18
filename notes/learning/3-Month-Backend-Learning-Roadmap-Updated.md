# 3-Month Backend Development Learning Roadmap

**Commitment:** Setup Phase (3-4 weeks) + 10 hours/week for 3 months (120 hours total)
**Starting Level:** Total newbie - no programming experience required
**Goal:** Build backend for your project idea
**Focus:** Backend/APIs with Node.js

---

## Overview

This roadmap takes you from total beginner to building production-ready APIs. You'll start with a 3-4 week setup phase to learn Git and JavaScript basics, then dive into backend development. By month 3, you'll be building your actual project with Claude Code as your coding partner.

**Philosophy:** Learn by building. Every week includes hands-on projects. Focus on progress, not perfection.

---

## Setup Phase (3-4 Weeks Before Month 1)

### Why This Phase Matters
You're starting as a total newbie - that's actually an advantage because you won't have bad habits to unlearn. This setup phase gets you comfortable with the tools and basic concepts before diving into the actual backend development roadmap.

### Your Learning Advantage
You already think in systems (security infrastructure, networking, containers). Programming is just another system with rules and patterns. Apply the same systematic approach you use for your kettlebell training and Stoicism practice - consistent daily reps, focusing on the process not the outcome.

### Week 1-2: Git Fundamentals

**Goal**: Understand the basic Git workflow and get comfortable with version control

**Core Commands to Practice**:
- `git init` - start a new repository
- `git add` - stage changes
- `git commit -m "message"` - save changes with a description
- `git status` - see what's changed
- `git log` - view commit history
- `git branch` and `git checkout` - manage different versions
- `git push` / `git pull` - sync with GitHub

**Daily Practice (30-60 minutes)**:
1. Create a test repository on your computer
2. Make random files, commit changes daily
3. Push to GitHub (create account if needed)
4. Use VSCode's Source Control panel to *see* what git does while typing terminal commands
5. Don't try to memorize - focus on the workflow: edit → add → commit → push

**Resources**:
- "Git in 15 Minutes" YouTube tutorial (search for highest viewed recent one)
- GitHub's interactive Git tutorial
- VSCode GitLens extension (shows git info inline)

### Week 2-3: JavaScript & Node.js Basics

**Goal**: Learn just enough JavaScript to start Month 1 - you'll learn more as you build

**Essential JavaScript Concepts**:
- Variables: `let` and `const`
- Data types: strings, numbers, booleans, arrays, objects
- Functions (regular and arrow functions: `() => {}`)
- Conditionals: `if/else` statements
- Loops: `for` and `while`
- **Async/await** - crucial for backend work (don't worry if it's confusing at first)

**Node.js Specifics**:
- Understanding `npm install` and `package.json`
- How `require()` and `import` work for modules
- Running scripts with `node filename.js`
- Using `console.log()` for debugging

**Daily Practice (30-60 minutes)**:
1. Keep a `practice.js` file open in VSCode
2. Write small code snippets to test concepts
3. Run them with `node practice.js` in the terminal
4. Break things on purpose to learn from error messages

**Resources** (pick ONE, don't do all):
- freeCodeCamp's JavaScript Basics (free, structured, hands-on)
- JavaScript.info - read "The JavaScript language" basics section
- The Odin Project - Foundations: JavaScript Basics

**Anti-Pattern to Avoid**: Don't fall into "tutorial hell" - get the basics then move to Month 1. You'll learn more by building and googling errors than reading documentation.

### Week 3-4: VSCode Setup & First Steps

**Essential VSCode Extensions**:
- **ESLint** - catches errors and enforces code style
- **Prettier** - auto-formats code consistently  
- **Thunder Client** or **REST Client** - test APIs without leaving VSCode
- **MongoDB for VS Code** - visual database interface
- **Docker** - container management (you're familiar with this)
- **GitLens** - makes Git visual

**VSCode Tips for Beginners**:
- Integrated terminal (Ctrl+` or Cmd+`) - run Node and Git here
- Source Control panel (Ctrl+Shift+G) - visual Git interface
- IntelliSense - code completion as you type (just start typing and accept suggestions)
- NPM Scripts panel - click to run package.json scripts instead of typing
- Debugging - set breakpoints by clicking left of line numbers

**Practical First Steps** (do these today):
1. Install Node.js from nodejs.org (choose LTS version)
2. Open VSCode terminal, type `node --version` to confirm install
3. Create `hello.js` with `console.log("Hello World")`
4. Run `node hello.js` - you're now a programmer!
5. Install Git, run `git --version` to confirm
6. Make a GitHub account if you don't have one
7. Install the extensions listed above

### Integration With Your Existing Systems

**Obsidian Journaling**:
- Document code snippets, "aha moments", and errors you solved
- Create a "Learning Log" note with daily entries
- Use code blocks with syntax highlighting: ```javascript

**Daily Habit Stack**:
- Treat coding practice like kettlebell training - show up daily even if just 30 minutes
- Can do before or after your workout routine
- Apply Stoicism principles: focus on process (daily practice) not outcome (being "good")

**Realistic Time Commitment**:
- Minimum: 30 minutes/day, 5 days/week
- Ideal: 60-90 minutes/day
- Weekends: Longer sessions if you want to go deeper

### Success Metrics for Setup Phase

You're ready for Month 1 when you can:
- [ ] Create a GitHub repo, clone it, make changes, commit, and push
- [ ] Write a simple JavaScript function and run it with Node
- [ ] Understand what `npm install` does
- [ ] Navigate VSCode confidently (terminal, files, extensions)
- [ ] Read basic JavaScript syntax without panic
- [ ] Use `console.log()` to debug simple issues

### Mindset for Learning

**Expect This**:
- Feeling lost and frustrated (that's normal, not a sign you're bad at this)
- Making tons of syntax errors (everyone does)
- Googling constantly (professional developers do this daily)
- Imposter syndrome (ignore it)

**Remember This**:
- You learned Gallagher, Milestone XProtect, n8n - you can learn this
- Every expert was once a confused beginner
- The goal isn't perfection, it's progress
- Breaking things is how you learn

---

## Month 1: Backend Fundamentals (40 hours)

**Goal:** Understand how servers, APIs, and databases work. Build simple APIs.

### Week 1: Server Basics & Node.js Review (10 hours)
**Hours breakdown:** 6h learning + 4h project

**Prerequisites:** You should have completed the Setup Phase and be comfortable with:
- Git basics (commit, push, pull)
- JavaScript fundamentals (variables, functions, if/else, loops)
- Running Node.js scripts
- VSCode navigation

**Learn:**
- Quick review of JavaScript concepts you'll use heavily:
  - Objects and arrays (you'll use these constantly for data)
  - Arrow functions `() => {}`
  - Async/await (critical for backend)
- HTTP basics: GET, POST, PUT, DELETE
- What is an API? Request/response cycle

**Resources:**
- Node.js: [Node.js Crash Course](https://www.youtube.com/watch?v=fBNz5xF-Kx4) (90 min)
- Python: [Python for Beginners](https://www.youtube.com/watch?v=rfscVS0vtbw) (4.5h)
- [HTTP Basics](https://www.youtube.com/watch?v=iYM2zFP3Zn0) (30 min)

**Project:** Hello World API
- Build a simple API with 3 endpoints:
  - GET /hello → returns "Hello World"
  - GET /time → returns current server time
  - POST /echo → echoes back what you send it

**Claude Code Usage:**
- Ask: "Explain how HTTP GET and POST requests differ"
- Ask: "Walk me through this Express.js code line by line"
- **Don't** ask it to build the project yet - code it yourself first

---

### Week 2: Data Handling & JSON (10 hours)
**Hours breakdown:** 4h learning + 6h project

**Learn:**
- JSON format and why APIs use it
- Parsing request bodies
- Input validation basics
- Error handling (try/catch, status codes)

**Resources:**
- [JSON in 10 Minutes](https://www.youtube.com/watch?v=iiADhChRriM)
- [REST API Tutorial](https://www.youtube.com/watch?v=Q-BpqyOT3a8) (90 min)

**Project:** Todo List API (no database yet)
- Create CRUD endpoints for todos:
  - GET /todos → list all todos
  - POST /todos → create new todo
  - PUT /todos/:id → update todo
  - DELETE /todos/:id → delete todo
- Store todos in memory (array)
- Add input validation

**Claude Code Usage:**
- "Review my todo API code. What could go wrong?"
- "How should I structure error responses in REST APIs?"
- Use it for debugging when stuck, but try solving problems first

---

### Week 3: Databases - SQL Basics (10 hours)
**Hours breakdown:** 5h learning + 5h project

**Learn:**
- What are databases? SQL vs NoSQL
- SQLite basics (start simple)
- CRUD operations in SQL
- Connecting your API to a database

**Resources:**
- [SQL Tutorial](https://www.youtube.com/watch?v=HXV3zeQKqGY) (4h - watch at 1.5x)
- Skip to practical sections (CREATE, INSERT, SELECT, UPDATE, DELETE)

**Project:** Todo List API v2 (with database)
- Migrate your Todo API to use SQLite
- Learn basic SQL queries
- Use an ORM (Prisma for Node.js, SQLAlchemy for Python) or raw SQL

**Claude Code Usage:**
- "Explain the difference between ORM and raw SQL"
- "Review my database schema for a todo app"
- Ask it to help convert your in-memory API to use SQLite

---

### Week 4: Authentication Basics (10 hours)
**Hours breakdown:** 5h learning + 5h project

**Learn:**
- Passwords: hashing with bcrypt
- Sessions vs JWT tokens
- Basic authentication flow
- Middleware concepts

**Resources:**
- [JWT Authentication Tutorial](https://www.youtube.com/watch?v=mbsmsi7l3r4) (90 min)
- [Password Hashing Explained](https://www.youtube.com/watch?v=cczlpiiu42M) (15 min)

**Project:** Add User Authentication to Todo API
- User registration endpoint
- Login endpoint (returns JWT)
- Protect todo endpoints (require authentication)
- Each user sees only their own todos

**Claude Code Usage:**
- "Ultrathink about the security implications of my authentication code"
- "What are common authentication vulnerabilities I should avoid?"
- Let Claude help implement JWT middleware

---

## Month 2: Intermediate Backend (40 hours)

**Goal:** Production-ready patterns, deployment, testing, and real-world scenarios.

### Week 5: Environment Configuration & Secrets (10 hours)
**Hours breakdown:** 3h learning + 7h project

**Learn:**
- Environment variables (.env files)
- Configuration management
- Keeping secrets out of code
- Different environments (dev, staging, production)

**Resources:**
- [Environment Variables Explained](https://www.youtube.com/watch?v=5iWhQWVXosU) (20 min)
- Read about the 12-factor app methodology

**Project:** Refactor Previous Projects
- Add .env files to all projects
- Move all secrets (DB credentials, JWT secrets) to environment variables
- Create .env.example files
- Add .gitignore rules

**Claude Code Usage:**
- "Audit my code for hardcoded secrets"
- "Show me best practices for managing configuration"

---

### Week 6: API Design & Documentation (10 hours)
**Hours breakdown:** 4h learning + 6h project

**Learn:**
- RESTful API design principles
- Status codes (200, 201, 400, 401, 404, 500)
- API versioning
- API documentation (Swagger/OpenAPI)

**Resources:**
- [REST API Design Best Practices](https://www.youtube.com/watch?v=0oXYLzuucwE) (30 min)
- [Swagger/OpenAPI Tutorial](https://www.youtube.com/watch?v=pRS9LRBgjYg) (20 min)

**Project:** Build a Blog API
- Posts CRUD (with authentication)
- Comments on posts
- Proper status codes
- Clear error messages
- Generate Swagger documentation

**Claude Code Usage:**
- "Review my API design. Does it follow REST principles?"
- "Help me generate OpenAPI/Swagger docs for my endpoints"
- Use explore-plan-code workflow: ask it to plan first, then implement

---

### Week 7: Testing Your Code (10 hours)
**Hours breakdown:** 4h learning + 6h project

**Learn:**
- Why testing matters
- Unit tests vs integration tests
- Testing frameworks (Jest for Node.js, pytest for Python)
- Test-Driven Development (TDD) basics

**Resources:**
- [Testing Tutorial](https://www.youtube.com/watch?v=r9HdJ8P6GQI) (40 min for Node.js)
- [Python Testing](https://www.youtube.com/watch?v=6tNS--WetLI) (50 min for Python)

**Project:** Add Tests to Blog API
- Write unit tests for key functions
- Write integration tests for API endpoints
- Aim for 70%+ code coverage
- Set up test scripts in package.json

**Claude Code Usage:**
- **This is where Claude Code shines!**
- "Write tests for my blog API endpoints. Make them fail first."
- "Now implement features to make the tests pass"
- Use the TDD workflow from the definitive guide

---

### Week 8: Deployment & DevOps Basics (10 hours)
**Hours breakdown:** 5h learning + 5h deployment

**Learn:**
- What is deployment?
- Platform options (Railway, Render, Fly.io, DigitalOcean)
- Environment variables in production
- Basic monitoring and logging

**Resources:**
- Pick a platform and follow their getting started guide
- [Deploying Node.js Apps](https://www.youtube.com/watch?v=l134cBAJCuc) (30 min)

**Project:** Deploy Your Blog API
- Choose a hosting platform (start with Railway or Render - free tier)
- Deploy your blog API
- Set up production environment variables
- Test your live API with Postman/curl
- Add basic logging

**Claude Code Usage:**
- "Help me create a deployment checklist for my API"
- "What security considerations should I have for production?"
- Ask it to review your production configuration

---

## Month 3: Build Your Project (40 hours)

**Goal:** Apply everything you've learned to build your actual project idea.

### Week 9: Project Planning & Architecture (10 hours)
**Hours breakdown:** 10h planning (no coding yet!)

**Activities:**
- Define your project requirements
- Design your database schema
- Plan your API endpoints
- Create user stories
- Set up development environment

**Deliverables:**
- Written requirements document
- Database ERD (Entity Relationship Diagram)
- API endpoint list with descriptions
- Tech stack decisions

**Claude Code Usage:**
- **Use Plan Mode extensively**
- "Ultrathink about the architecture for [describe your project]"
- "Review my database schema. What problems might I encounter?"
- "What are the edge cases I should handle?"
- Create a CLAUDE.md file for your project with architecture notes

---

### Week 10-11: Build MVP (20 hours)
**Hours breakdown:** 20h implementation

**Focus:** Build the core features of your project

**Approach:**
- Start with database setup
- Build one feature at a time
- Write tests as you go
- Use git branches for features
- Commit frequently

**Claude Code Workflow:**
Use the **Explore-Plan-Code-Commit** pattern:
1. **Explore:** "Read my current code structure. Don't write anything yet."
2. **Plan:** "Think hard about how to implement [feature]. What files need to change?"
3. **Code:** "Implement the plan. Write tests first."
4. **Commit:** "Commit these changes with a descriptive message."

**Tips:**
- Build features in order of dependencies
- Get each feature working before moving to the next
- Don't try to do everything at once
- Use `/clear` between major features in Claude Code

---

### Week 12: Polish & Deploy (10 hours)
**Hours breakdown:** 5h polish + 5h deployment

**Activities:**
- Add error handling everywhere
- Improve validation
- Write API documentation
- Add logging
- Deploy to production
- Test thoroughly

**Claude Code Usage:**
- "Audit my code for missing error handling"
- "Generate comprehensive API documentation"
- "What should I check before deploying to production?"
- Use the reviewer-implementer pattern: one Claude session reviews, another fixes

---

## Weekly Routine Structure

**Suggested 10-hour weekly schedule:**

**Weekday sessions (Mon-Fri):** 1.5h each = 7.5h
- 30 min: Review previous day, read/watch learning materials
- 45 min: Hands-on coding
- 15 min: Document what you learned, note questions

**Weekend session (Sat or Sun):** 2.5h
- Longer project work session
- Finish the week's project
- Plan next week

---

## Claude Code Integration Throughout

### Month 1: Use Claude as a Teacher
- Ask conceptual questions
- Request explanations of your code
- Debug when truly stuck (after trying yourself for 20+ min)
- **Don't** let it write everything for you

### Month 2: Use Claude as a Senior Developer
- Code reviews
- Architecture feedback
- Test writing (TDD approach)
- Security audits
- Start using Plan Mode more

### Month 3: Use Claude as a Pair Programmer
- Explore-Plan-Code-Commit workflow
- Parallel work (you build one feature, Claude builds another)
- Full utilization of CLAUDE.md
- Custom slash commands for your project

---

## Essential Resources

### Documentation Bookmarks
- [MDN Web Docs](https://developer.mozilla.org/) - JavaScript reference
- [Node.js Docs](https://nodejs.org/docs/) - Official Node.js documentation
- [Express.js Guide](https://expressjs.com/en/guide/routing.html) - Express framework
- [PostgreSQL Tutorial](https://www.postgresqltutorial.com/) - When you outgrow SQLite

### Community & Help
- [Stack Overflow](https://stackoverflow.com/) - Q&A for specific problems
- [Dev.to](https://dev.to/) - Tutorials and discussions
- [Reddit r/learnprogramming](https://www.reddit.com/r/learnprogramming/) - Beginner-friendly community

### Tools You'll Use
- **VS Code** - Code editor
- **Postman** or **Insomnia** - API testing
- **DBeaver** or **TablePlus** - Database GUI
- **Git** - Version control
- **Claude Code** - Your AI pair programmer

---

## Success Metrics

### End of Month 1
- [ ] Built 3 working APIs (Hello World, Todo, Todo with DB)
- [ ] Comfortable with your chosen language basics
- [ ] Understand HTTP methods and status codes
- [ ] Can write basic SQL queries
- [ ] Implemented basic authentication

### End of Month 2
- [ ] Understand environment configuration
- [ ] Can design RESTful APIs
- [ ] Written tests for your code
- [ ] Deployed at least one API to production
- [ ] Comfortable using Claude Code for reviews

### End of Month 3
- [ ] Your project MVP is built and deployed
- [ ] Written tests for critical functionality
- [ ] API is documented
- [ ] Using git effectively (branches, commits, PRs)
- [ ] Can explain your architecture decisions

---

## Common Pitfalls to Avoid

1. **Tutorial Hell:** Don't just watch videos. Code along, then build something different.

2. **Letting Claude Do Everything:** You won't learn. Fight with the problem for 20 minutes before asking.

3. **Skipping Tests:** They seem like extra work but save you hours debugging later.

4. **Not Using Git:** Commit often. Even if it's just you, practice good habits.

5. **Overengineering:** Build the simplest thing that works, then improve it.

6. **Ignoring Errors:** Read error messages carefully. They're trying to help.

---

## After the 3 Months

Once you complete this roadmap:

**Continue Learning:**
- Frontend basics (React, Vue, or Svelte) to become full-stack
- Advanced database concepts (indexes, queries optimization)
- Containerization (Docker)
- Advanced authentication (OAuth, SSO)
- GraphQL as an alternative to REST
- Microservices architecture

**Build More Projects:**
- Rebuild your project with a different tech stack
- Contribute to open source
- Build side projects that solve real problems

**Job-Ready Path (if that's your goal):**
- Build 2-3 portfolio projects
- Contribute to open source
- Network in developer communities
- Start applying for junior positions

---

## Your Project Idea

**Take 30 minutes right now to answer:**

1. What are you building?
2. Who is it for?
3. What problem does it solve?
4. What are the 3 core features?

Write this down in a new note: `My-Project-Idea.md`

Having a clear vision will keep you motivated through the 3 months.

---

## Final Tips

**Stay Consistent:** 10 hours/week beats 20 hours one week and 0 the next.

**Build in Public:** Share your progress. Twitter, Dev.to, or a blog. Accountability helps.

**Don't Compare:** Other people's "day 30" might be their day 300. Focus on your progress.

**Ask for Help:** Stuck for more than an hour? Ask Claude Code, Stack Overflow, or Reddit.

**Celebrate Wins:** Shipped your first API? Awesome! Fixed a hard bug? Victory! Acknowledge progress.

---

**Ready to start?**

Go to Week 1, pick your language (I recommend Node.js), and build your first API. You've got this!

---

## Checklist: Before You Begin

- [x] Claude Code installed and authenticated
- [x] Code editor installed (VS Code recommended)
- [ ] Git installed and basic config set up
- [ ] Node.js or Python installed
- [ ] Created this learning folder in your Obsidian vault
- [ ] Blocked out 10 hours in your calendar each week
- [ ] Read through this entire roadmap once
- [ ] Documented your project idea in My-Project-Idea.md

**Start Date:** _______________
**Target Completion:** _______________

Good luck! 🚀
