---
theme: gaia
_class: lead
paginate: true
backgroundColor: #fff
backgroundImage: url('https://marp.app/assets/hero-background.svg')
---

# **Hellbender Crash Course**

Hi John! :)

---

## What is Hellbender?

- In short, Hellbender is V2 made from scratch
- The team was unable to resolve an error, which caused progress to stall for ~6 weeks
- During this period, Cole and Ian elected that we just nuke V2 and start from scratch
  - Adam was elated

---

## What went wrong with V2?

- V2's poorly documented foundation was designed by Dr. Ignacio & Alex Bowen, who are no longer with us
  - This made it exceedingly difficult to add features
  - IMO it was more difficult to work on than Legacy
- V2's frontend design didn't render anything on the server, whereas Legacy renders everything on the server
  - The team suffered a huge learning curve required to develop a fully client-side frontend
  - Especially when transitioning from something like PHP

---
# **The Eternal Enemy: Complexity**

![bg right:30% contain](https://grugbrain.dev/grug.png)

> apex predator of grug is complexity
> complexity bad
> say again:
> complexity *very* bad
> you say now:
> complexity *very*, *very* bad
>
>[&ndash; grug](https://grugbrain.dev/)

---

> given choice between complexity or one on one against t-rex, grug take t-rex: at least grug see t-rex
>
> one day code base understandable and grug can get work done, everything good!
>
> next day impossible: complexity demon spirit has entered code and very dangerous situation!
>
> grug no able see complexity demon, but grug sense presence in code base
>
> *&ndash;grug*

---

## Thinking Like Grug

- **Keep it simple, stupid!!**
- Choose popular, battle-tested, tools with ample documentation
  - ["MongoDB is Web Scale"](https://www.youtube.com/watch?v=b2F-DItXtZs)
- Tools with opinonated design have a clear "right way"
  - Next.js is a *server-side* React framework
  - Electing to not use any server-side features invites *complexity demon*
- Don't suffocate yourself with "generics"
  - Using something in under 3 places? Copy and pasting is OK!

---

## Hellbender's Stack

- Next.js
- PostgresSQL
- Prisma

<br />

> That's it?
>
> *&ndash; You*

---

## Next.js

- Most popular React framework
- Excellent for developing simple CRUD apps
- Extremely well documented
- Was already being used for V2
- Less useEffects, more better

<!-- _footer: Source: [Stack Overflow Developer Survey 2025](https://survey.stackoverflow.co/2025/technology#2-databases)' -->

---

## Prisma

- Single source of truth for database & application models
- Queries not classes, no complex model objects
- Healthy opinionation to prevent anti-patterns
- Type-safe queries validated at compile time
- Good developer experience
  - Less boilerplate, more auto-complete
  - Data migrations
- Excellent support for "raw" queries

<!-- _footer: Paraphrased: [Why Prisma ORM?](https://www.prisma.io/docs/orm/overview/introduction/why-prisma) -->

---

## PostgresSQL

- Most popular, admired, and desired database
- Wider range of datatypes
  - Booleans, Arrays, JSONB, UUID, Text Search
- First-class compatiblity with Prisma
- Nearly identical syntax to MariaDB

<!-- _footer: Source: [Stack Overflow Developer Survey 2025](https://survey.stackoverflow.co/2025/technology#2-databases)' -->

---

## Hellbender's Libraries

These libraries are not required to be used, but will make your life easier and prevent redundant work.
- TypeScript (JS + types)
- Tailwind CSS (utility classes)
- TanStack Form (webform state & validation)
- Valibot (runtime data validation)
- Shadcn UI (copy-and-paste customizable UI components with great a11y)

---

## Where are we now?

Since starting Hellbender, we have achieved:
- Well-structured project with local dev environment
- Customized accessible component library
- App Migrations: Calendar, Warehouse
- Microsoft SSO
- Test Server
  - Docker image created, ready for internal deployment


---

## Where are we now? (cont.)
- Data migrations: Version controlled schema & seed data
- Integration with Legacy databases
  - Easy workflow for creating Prisma schemas from exisiting tables
  - B0149 sync for users
  - Org Chart with search


---

## Where *aren't* we now?

Compared to V2:

- npm is a PowerShell script, so those without a dev environment in WSL cannot install Node.js packages.
  - They don't NEED to use WSL, but we'd have to get DIT to approve a running .ps1 scripts (or a specific script, idk)

Compared to Legacy:

- No test server yet
