📊 NaijaBased - Architecture Diagrams
This folder contains system architecture and database diagrams for the NaijaBased platform.

📁 Diagram Files
File	Description	Format
system-architecture.md	Complete system architecture diagram	Mermaid
database-er-diagram.md	Complete database entity relationship diagram	Mermaid
🖼️ How to View
These diagrams are written in Mermaid syntax, which is natively supported by GitHub. Simply view the .md files directly on GitHub and the diagrams will render automatically.

🛠️ Creating PNG/SVG Versions
If you need image files (PNG/SVG) for presentations or offline viewing:

Option 1: Mermaid Live Editor
Go to https://mermaid.live

Copy the diagram code from the .md files

Paste into the editor

Export as PNG or SVG

Option 2: Mermaid CLI
bash
# Install Mermaid CLI
npm install -g @mermaid-js/mermaid-cli

# Convert to PNG
mmdc -i system-architecture.md -o system-architecture.png
mmdc -i database-er-diagram.md -o database-er-diagram.png
Option 3: GitHub Markdown
Simply view the .md files on GitHub - they render automatically! 🎉

📋 Diagram Index
System Architecture Diagrams
High-Level Architecture - Complete system stack

Data Flow Sequence - Request lifecycle

Deployment Architecture - AWS infrastructure

CI/CD Pipeline - Build and deploy process

Database Diagrams
Core User Tables - Users, posts, comments, follows

Business & Marketplace - Listings, items, reviews

Events & Ticketing - Events, bookings, tickets

Jobs - Job posts, applications

Communities - Groups, members, posts

Payments - Orders, transactions, escrow

Messaging - Conversations, messages

Notifications - Alerts, preferences

Taxonomy - Hashtags, categories

Location - Nigerian states, LGAs, cities

🔄 Updating Diagrams
When making changes to the architecture:

Update the relevant .md file

Test rendering in Mermaid Live Editor

Commit both the .md source and exported PNG (optional)

Last updated: February 2026