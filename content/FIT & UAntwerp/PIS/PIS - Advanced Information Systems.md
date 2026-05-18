# Information systems
- a really important part of the business, driving productivity and business 
- companies also spend a lot of capital on IT systems, because they enable them to be more effective and create the competitive advantage
	- but it needs to be done "the right way", so the investment pays off and the company gains more out of the IT than it pays
		- this is also, what is the IT Governance about (see [[UA - Digital Risk & Security - hub]])
### Digital firms
- information systems enable the existence of "digital firms", which offer
	- digital services
	- the interactions with customers are digital
	- almost all core business processes are handled digitally
	- having a seamless flow of information within the company (information is available everytime and everywhere)
	- company is able to be more responsive and agile

> **An information system** is a set of interconnected components that 1) collect 2) process 3) store and 4) distribute information to support decision-making and control within the organization. It also contains a feedback loop (for correcting the inputs from evaluating the outputs).
- and then this information systems is able to communicate and connect with suppliers, customers, competitors, stakeholders and employees
- an important note: the information system is not just about the technology, but it also has the socio-technical perspective (business, tech, management)
- there is a distincition between data (a stream of raw facts) and information (clusters of facts useful for decision making)
	- the goal of the IT system is to transform data into useful information 
- there is also a distinction between:
	- computer literacy (knowing how the IT system works technically)
	- information systems literacy (a broader understanding of the IS and the behavioral knowledge (organizational behavior and management))
### Value chain
- a chain of processes and activities that add up to the final value of the product/service for the customer (the more value, the more margin for the company)
	- primary activities (that create the value directly): inbound logistics -> operations -> outbound logistics -> marketing and sales -> service
	- secondary activities (they support the primary activities): HR, firm infrastructure, culture, technology etc.
- and the information system could support specific parts of the value chain to be more effective/productive (CRM system for sales, WMS for logistics etc.)
### 3 main dimensions/functions of the IS
- 1) Organizations
	- structure, hierarchy, culture, business processes, politics
	- organization and information system affect each other (if I drop a new ERM, it will shape the company and vice versa (the organization structure will determine, which ERM is going to be used))
	- business processes inside the organization: IT system supports various areas inside the company/organization (sales, accounting, HR, finances etc.)
- 2) Management
	- IT system creates a value for managers, who then do real decisions based on the data available, who can plan accordingly and innovate new processes
- 3) Technology
	- includes physical hardware and software, storage media and communications/network technology to allow the functioning of the IT system
- the main point is also that the organization and the IS shape each other (they affect the other)
	- from the behavior of managers, to the technology used and which processes are current in the business
### 3 main levels of IS
- Operational level (the lowest one) - TPS - Transaction Processing System
	- IS supports the operational managers, keeping track on the day-to-day activities and statistics (payrolls, order processing, shipping, billing, record managers)
	- those are critical systems, if it fails for a few hours, the business stops working
	- this system also serves as the foundational data source for all other systems
- Management level (the middle one) - MIS - Management Information System + DSS - Decision Support System
	- IS serves the monitoring, controlling, decision-making and administrative activities (middle managers)
	- MIS - uses aggregated transaction data to create summary reports (annual budgeting)
		- for routine, scheduled reports with predefined questions and limited analytical flexibility
		- assisting in general control of the business, supports routine decisions
	- DSS - interactive processing of the transactional data for professionals to make informed decisions and be able to model different situations
		- for non-routine, changing conditions and decisions (utilizing data models for modelling various situations, "what-if" scenarios, ad-hoc queries etc.)
		- e.g. competitor pricing models
		- Model-driven DSS - usage of analytical models for specific purpose (competitor pricing)
		- Data-driven DSS - analyze large pools of data (e.g. whole sales data), blurs into Business Intelligence (BI)
- Strategic level (the top one) - ESS - Executive Support System
	- IS helps the senior managers to tackle and address strategic issues
	- involves summarized and processed data from TPS, DSS and MIS + external events (new tax events, economic trends, geopolitical events)
		- but the executives are also able to "drill-down" the data to look closely (if e.g. something is not adding up)
	- present the big picture for strategic decisions and long-term planning
	- often used methodology: BPM (Business Performance Management) which systematically translates the firm's strategies into defined KPIs
		- which are then overseen by the senior management
- traditional companies have these systems separate, which causes inefficiencies, whereas modern, digital companies have these systems interlinked with data/information flows 
	- this way, new effective (innovative) business processes can be established
	- that is why ERP exists, we do not want separate TPS for sales, TPS for HR, TPS for finance etc. (unmanageable, inconsistent data)
### Business process
> Manner in which work is organized, coordinated and focused to produce a valuable product or service.
- my words: a sequence of actions, data flows, material flows and knowledge that is composed together to produce valuable product or service in the end
	- or something that supports the business process which creates the value for the company
- IS play a vital role here: they are able to automate parts of the business processes, add more data/knowledge etc. and also, we are able to build entire new processes with IS
- there are also "Cross-functional" business processes - which span multiple departments (sales, marketing, manufacturing etc.) and different teams of people to complete a piece of work 
	- e.g. an Order Fulfillment Process
### Enterprise Systems
- designed to support organization-wide process coordination and integration
- implementing these systems is not only a technological challenge, but also a managerial one:
	- managers have to find a "consensus" across the whole company on how the IS should look like (the marketing team has different opinions than the operations team)
	- managers have to manage a large-scale employee training (to teach them to use the new IS)
	- managers have to account for the costs of the IS and the value it brings into the company (is there a return on the investment?)
	- there are also high "switching costs", when a company has a vendor lock-in in some IS and switching to another will cost a lot of resources (time, money...)
##### ERP = Enterprise Resource Planning
- a single source of truth for organization-wide information, coordination and integration of business processes (interconnection of multiple applications with a shared central database)
- 4 main parts:
	- finance and accounting, HR, manufacturing and production, sales and marketing
- ERP systems can be really complex, it may be beneficial to change the processes in the firm to match with predefined processes in the ERP (as they often mirror the industry "best practices") othervise, the performance could degrade
- benefits:
	- unify the firm's structure and organization
	- have all key processes under one hood
	- access to all important information at one place
		- in a single, comprehensive, central database
		- faster decision making, single source of truth, information sharing
	- unified platform in terms of technology 
- challenges:
	- difficult to build (will change the way the business operates)
	- is technologically complex
	- centralized organization coordination and decision making
##### SCM = Supply Chain Management
- manage buying the materials, transformation to products and distribution to customers (+ reverse logistics (returns, refunds))
	- e.g. order storing and tracking, checking inventory availability, track shipments, plan production based on the demand
- Just-in-time strategy: minimize inventory by having components arrive exactly when needed
- Bullwhip effect
	- this effect describes the disortion of demand information as it moves upstream through the supply chain
		- e.g. it acts like a "telephone game" as each signal transition disorts as it passes through the layers
		- it acts like a effect amplifyer: a small fluctuation in customer demand gets amplified at each layer (retail to distributor to manufacturer to supplier) causing big over- or under-stocking upstream
	- caused by prediction errors, batch ordering, price fluctuations, geopolitical events etc. - SCM tries to minimize it
- two models:
	- Push-based model: driven by forecasts
		- e.g. manufacture 1000 sofas and hope that customers buy it
	- Pull-based model: driven by actual customer orders
		- wait for an order (e.g. custom-made laptops) and then assemble it and sell it 
	- a lot of companies actually combine these two models, and IS have enabled a wide spread of pull-based models, so they could be effective and scalable as well
- supply chain is often split into two main parts:
	- upstream (suppliers and their suppliers)
	- downstream (distributors and retailers)
##### CRM = Customer Relationship Management
- manages how the company deals with existing and new customers to optimize revenue, satisfaction, retention and mainly, consolidating information from all over the organization to one single place 
	- end-to-end customer care
	- consolidates customer data from multiple sources (also called "touch points") and provides analytics for management
		- websites, telephones, emails, live chats, social media, mail, customer service desk, retail store
	- special focus on retaining customers in order to drive sales
		- it costs way more to get a new customer than keeping the existing one
- a good CRM provides a 360 degree view of the customer
- we have:
	- Operational CRM = customer-facing applications (call center, marketing etc.) and daily interactions, involves:
		- Sales Force Automation: tools for sales representatives (contacts, opportunities, sales forecasting...)
		- Customer service: call center, knowledge base to solve common problems, service desk...
		- Marketing: campaign management, lead tracking, cross-sell, A/B testing
	- Analytical CRM = analyzing customer data (to select profitable customers, churn rate (a percentage of customers who stop using the services/products))
		- major part: CLTV = Customer Lifetime Value (comparing the revenue from the customer vs. costs of acquiring and servicing him/her)
			- this is the main customer-related metric
			- formula: (annual revenue per customer - annual cost to serve) x expected number of years of relationship - acquisition cost
			- 20% of customers create the 80% of the profit (they should have special treatment)
		- involves:
			- Customer segmentation (based on the geography, value, behavior..)
			- Cross-sell, up-sell analysis (recommend what to buy next)
			- Churn prediction (which customers are likely to leave, enabling proactive retention)
		- ETL (extract, transform, load) process of customer data to the common data warehouse to allow for deep Business Intelligence over that data
		- types of analyses:
			- response analysis: analysing the results of the campaigns (in real time), allowing correct the marketing campaigns irl
			- profile analysis: comparing profiles of responding customers to the intended target profile
- extensions of CRM:
	- ERM = Employee Relationship Management
		- handling employee performance, training and compensation
		- HR-adjacent
	- PRM = Partner Relationship Management
		- information sharing with distributors and retailers
- Campaign Management
	- = series of interconnected promotional efforts designed to achieve defined marketing goals (within some defined timeframe)
	- CRM helps to identify the right customer segments, the right media channels, the right time, the right complementary products (cross-sell) etc.
	- campaign process:
		- 1) planning, 2) development (target customer lists, target offer, media support etc.), 3) execution/rollout, 4) analysis of the results
- current trends:
	- Social CRM = handling the company's presence on social media and networks (communication, brand strategy etc.)
	- Cloud-based and mobile CRMs = no need for big upfront investments to start using the CRM system (SaaS based)
	- VRM = Vendor Relationship Management, where customers can develop their own relationships with different vendors
		- the relationship is opposite, customers manager their vendors
	- dealing with GDPR, CCPA (California) etc.
- possible problems:
	- CRM may lead to favoritism within the audience of customers (which then leads to dissatisfaction among customers)
	- poor quality of information: duplicates, incomplete records, outdated information, GDPR
	- poor integration with other systems
##### KMS = Knowledge Management System
- the flow is: data (unstructured information from all sources) -> information (processed data organized into categories of understanding) -> knowledge (concepts, experience, insights that help to create and evaluate information) -> wisdom (collective experience of knowing when, where and how to apply knowledge to solve problems)
- knowledge is a big asset for the company 
	- it has the network effect (it's value increases as more people share it)
	- it requires a lot of managerial and organizational support (motivating employees to share their knowledge and experience across the company, motivating to use the shared databases, designated tools etc. (and not saving it on personal workspaces))
- organizational learning = the process of the whole organization learning from the changes in the environment and adjusting it's business processes to adapt
- 4 main system activities to manage knowledge:
	1. knowledge aquisition
	2. knowledge storage
	3. knowledge sharing/extending
	4. knowledge application
- 3 main types of KMS
	- Enterprise-wide knowledge management systems
		- general knowledge base for the whole company capturing structured knowledge (reports, presentations, formal documents) and semi-structured knowledge (voicemails, meeting notes, bulletins)
		- the need of taxonomy and tagging to organize the knowledge and make the retrieval easy
	- Knowledge Work Systems (KWS)
		- SW and HW platforms for knowledge workers
	- Intelligent techniques
		- knowledge discovery via data mining (patterns in large datasets)
		- expert systems (capturing unstructured knowledge in specialized domains)
### Organization
- high-level "technical" definition of an organization:
	- "stable, formal social structure that takes resources from the environment and processes them to produce outputs"
- behavioral definition of an organization:
	- "a collection of rights, privileges, obligations and responsibilities, that is delicately balanced over a period of time through conflict"
	- this is like a messy political coalitions where rights and obligations are negotiated
- all organizations share a set of common features:
	- clear division of labor (noone cannot do everything)
	- hierarchy 
	- explicit rules and routines (business processes are made from routines)
- about a relationship between IS and the Organization: it's a two-way street
	- IS are built by managers to serve the company, but at the same time, the company itself must be willing to change and adapt new technologies and possibilities of new information systems
	- the mediating factors could be: culture, environment, structure, business processes, politics etc.
- and also organizations shape the environment and the environment is shaping the organization
	- and the IS is an important part of the exchange (e.g. it determines, how the organization sees the environment)
- implementing IS in the company is highly political, it fundamentally changes who controls information and who has decision making power (and there could be a significant resistance to change in the company)
	- that is why the companies are not flexible and adaptive enough to move as fast as the technology changes 
##### Transaction cost theory
- IT lowers the transaction costs, allowing the company to grow without needing to hire more employees
	- transaction costs are things/services that the company needs to "buy/get" from the market (like searching for suppliers, negotiating contracts, monitoring quality, handling disputes etc.)
		- for these things, the company had to get it externally or hire a lot of employees to do it
		- with a good IS, a lot of things could be done for lower costs and internally
##### Agency cost theory
- IT also reduces the cost of supervising employees, leading to "flattening" of the company's organizational infrastructure
	- each manager can supervise more employees (dashboards, KPIs, automated reporting...)
	- lower-level employees can make decisions without supervision and therefore, increase the management efficiency
- agency cost = a cost of supervising someone else
### IS as an competitive advantage
- for small companies: get muscles and reach like larger companies
- for big companies: get agility and resposiveness like smaller companies
- Porter's Five Forces/Threats Model (and IS affects each force (e.g. it allows more firms to entry the market))
	- Threat of new market entrants 
		- IS enables them with disruptive technologies (e.g. Netflix vs. Blockbuster)
	- Threat of substitute products/services
	- Bargaining power of suppliers (how much leverage do they have?)
	- Bargaining power of customers (how much leverage do they have?)
	- Rivarly among existing competitors in the market
- [[#Value chain]] model
	- for highlighting which primary and which secondary activities add the most value
	- focused on the value of activities inside the company
- Value Web
	- something similar to the Value chain, but not for a single company, but for a network of mutliple companies working together to create value for the customer 
		- the main company + suppliers + partners etc.
	- this is more like modern, digital firms - orchestrating a web of firms working together rather than owning each step of the way and do everything internally
- Strategy for leveraging the Core competencies through IS
	- core competency is an activity, where the company excels as a world-class leader (and that is the reason the company is successful)
	- and the IS helps to share this competency across the whole firm more effectively (so the firm can utilize it more efficiently)
- Advantage of Network Economics (enabled by modern IS)
	- the value of the service increases quadratically as more users join (Metcalfe's Law)
	- and the marginal cost of adding one more user is almost zero (also thanks to IS)
		- e.g. one more Facebook user costs Facebook almost nothing
- Four generic competitive business strategies:
	- low-cost leadership (Wallmart)
	- product differentiation (Apple)
	- focus on market niche (Hilton or a luxury boutique hotel chain)
	- strenghten customer/supplier intimacy (Amazon)
- and for all of this, we are asking, how can IT help the company to lower the costs, differentiate the products and change the scope of the competition?
### Management challenge of implementing current IS systems
- once the advantage from implementing the system is achieved, it is difficult to sustain the advantage long-term
	- the companies are not able to change fast enough to accomodate new technologies
	- the competitors can easily copy the existing successful systems
- also, aligning the IT with business objectives is as great management challenge
- another challenge is the scalability of the IT infrastructure (to keep up with the business growth)
### IT infrastructure
- = a set of physical devices, software applications, services required to operate the entire enterprise, concretely 7 components
	- computer hardware platforms (Dell, HP, Apple, Linux machines)
		- mobile devices, servers, desktops, laptops
	- operating system platforms (Linux, MacOS, MS Windows, iOS)
	- enterprise software applications (SAP, Oracle, Microsoft)
		- systems like ERP or CRM
	- data management and storage (Oracle, SQL Server, MySQL)
	- networking and telecommunications platforms (Cisco, AT&T)
		- physical network equipment and services
	- internet platforms (Apache, Java, Microsoft .NET)
		- platforms supporting intranets, extranets, Web Services
	- consulting and system integration services (KPMG, Accenture)
		- consulting on how to integrate systems
- Moore's Law - about a microprocessing power
	- every 18 months, the processing power doubles (the number of transistors in a dense integrated circuit doubles) and the cost halves
	- how the eras went through time: mainframe/minicomputer, personal computer, client/server, enterprise internet computing, cloud computing and mobile computing
		- the trends is in shrinking the hardware
	- this trend is slowing due to physical limits (approaching the sizes of atoms) - so the trends are: multicore, specialized chips (TPUs, NPUs), GPUs
- Metcalfe's Law: the value of the network grows quadratically as more users connect, driving the rapid adoption of IT
- The law of mass digital storage = the cost of storing digital information halves every year (it doubles up and the cost stays the same)
### Grid computing
- connecting geographically remote computers into a single network to work in parallel on computationally-heavy business problems 
- it's about chaining thousands of smaller computers to a computing grid rather than buying a large mainframe
### Cloud = On-Demand computing
- off-loading processing power on large data centers 
- pay only for the computing power that the company actually uses
- saving high costs on infrastructure, handling big spikes in the demand
- SaaS - renting full applications (Salesforce, Google Workspace)
- IaaS - renting raw compute and storage, I can build whatever I want (AWS, Azure)
- PaaS - renting runtime environment, I only deploy code (Heroku, Google App Engine)
- deployment models:
	- public cloud, private cloud, hybrid cloud, community cloud
### Autonomic computing
- systems that are able to configure, optimize and tune themselves (because they are becoming too complex that it is expensive to manage them manually)
	- and also, the human error (the operator's error) is the most common cause of the crashes
- the systems can also heal themselves from a crash + protect themselves from outside intruders and self-destruction
### Other trends
- Edge computing - pushing computation closer to the data source (IoT devices, mobile users, factory floors) rather than centralizing it in the cloud
	- lower latency, real-time analytics, reducing bandwith costs
- Service-Oriented Architecture (SOA) and Web Services
	- not having one monolith, but having more loosely coupled applications communicating with each other via standard protocols (XML, SOAP, JSON, REST)
	- microservices are a different approach
		- SOA: dumb endpoints, smart pipes (ESB)
		- Microservices: dumb pipes, smart endpoints
	- using web technology to build intranets and standardize communication between applications
- Software Outsourcing
	- purchasing software packages
	- hiring external firm to build software for me
	- cloud-based services
- Green computing
	- using practices to minimize environmental impact
- Virtualization
- Middleware - to glue all apps in the organization together and enable them to communicate efficiently and quickly
### Other notes
- SLAs = Service Level Agreements (important for using cloud service)
- TCO = Total Cost of Ownership
	- total cost of owning IT infrastructure involves: hardware/software acquisitions, installation, training, support, maintenance, infrastructure, downtime, space, energy bills
	- hidden costs are typically 3-5 times higher than the purchasing cost
	- the relocation to cloud could significantly cut the IT infrastructure costs
# Decision making in the Digital firm
- all parts of the IS of the firm are helping to make a more informed decision (which makes the company to perform better)
	- two main terms:
		- Business Intelligence (BI) - infrastructure for collecting, storing and analyzing data (data warehouses, BI platforms)
		- Business Analytics (BA) - tools and techniques for analyzing and understanding data (online analytical processing, predictive models, statistics)
	- there is a great business value in an "improved decision", it may be just a few dollars, but it quickly compounds into millions
- there are different levels of decisions:
	- unstructured decision: non-routine, requires judgement, not clear decision based on the data (often done by senior management), e.g. approve capital budget, decide corporate objectives
	- structured decision: routine and use definite procedures (e.g. restocking based on today's demand) - often done by operational (lowest) management or individual employees and teams
	- semi-structured decisions (only part of the problem is clear based on the data, requires judgement, e.g. marketing plans) - often done by middle management
- 4 phases of the decision-making (by Herbert Simon):
	1. Intelligence: problem discovery, identificaton and it's understanding
	2. Design: identifying and exploring various solutions
	3. Choice: choosing among solution alternatives
	4. Implementation: implement the chosen alternative and monitor it
		- And repeat, if the alternative fails
- decision support systems
	- there is a difference between MIS, DSS and ESS systems (see [[#3 main levels of IS]]) to make decisions (each has a different purpose)
	- GDSS = Group Decision Support Systems
		- special systems for a group decision-making and problem solving
			- involving HW in conferences, SW (brainstorming and voting tools) and trained facilitators/session managers (people that are able to handle the process of the group dynamics and effective decision making)
			- because normal meetings become ineffective when too many people attend
### Challenges of a better decision making
- poor information quality (bad accuracy, completeness, timeliness)
- management filters (biases and bad decisions made by managers)
	- each person has subjective opinions and is rejective to data that do not conform to his opinions
- organizational inertia (strong forces within the organization resisting change)
- = investing into a better decision system does not guarantee better decisions in the real world
	- two different companies with the same BI tools could have wildly different results
	- the IT Governance is important, because if correctly applied, it links the technology to the organizational and managerial structure and ensures that it works together
	- the organizational and managerial support is more important than the technology behind
- some of the decision making problems could be solved by automated decision making systems (based solely on algorithms)
	- mainly the high-volume, structured decisions (dynamic pricing, credit approvals, ad bidding)
# How to develop a new IS so it fits the organization's business plan?
- building a new IS is not easy and it is a form of a planned organizational change
- before doing any changes, the organization needs to plan it using one or another (or a combination):
	- Strategic Analysis
		- top-down, looks over whole organization's requirements, how data are used across the whole firm
		- comprehensive, but expensive and can produce a 'generic data view'
	- CSFs (Critical Success Factors)
		- methodology, where managers individually select 3-6 factors which are critical to the success of the company and then the IS is being built around these factors
		- it is faster, focuses on what matters, but is narrow and the CSFs from different managers can differ 
### 4 kinds of structural change enabled by IT
- each has different risks and rewards, sorted from lowest risk to highest risk
1. Automation - mechanizing selected procedures to speed up existing tasks
	- the most common form of IT-enabled change
	- e.g. ATM automates the cash withdrawal, but the business process itself does not change
2. Rationalization of procedures - streamlining standard operating procedures
	- often happens after automation, while it reveals some obvious bottlenecks, we some changes to the processes are needed to streamline them
	- another drivers of rationalization
		- Total Quality Management - an iterative and incremental way of ensuring the quality of business processes (small improvements rather than one large improvement)
		- Six Sigma - a set of concrete methodologies and techniques to improve the quality of the processes and reducing costs
3. Business Process Reengineering (BPR) - analyzing and reducing workflows to reduce waste and repetitive tasks
	- complete/radical rework of some of the business procedures and processes
	- do not streamline existing processes, throw them out and start from scratch
4. Paradigm shift - a radical change of the nature of the business and the organization itself (big risk, big returns, high chance of failure)
	- e.g. Netflix changing the paradigm from DVD rental to content production
### Process of the IS building
- organizations follow a SDLC (Software Development Life Cycle), having core phases:
	- Requirements and Analysis
		- also should contain a feasibility study (to determine if the solution is financially, technically and organizationally achievable)
			- a so called Enterprise Analysis
		- most important part is: establish the information requirements (who needs what information, when, where and how)
		- define problem, identify causes, specify solutions, identify information requirements...
	- Design (design specifications, define, how the system will meet the requirements)
		- the end users need to be able to understand the system and effectively work with it, so it delivers the wanted business value (there is no value in the complex system nobody cannot properly use)
		- different approaches:
			- Object-Oriented Approach - combining data and processes together
			- UML Case Diagrams - defining responsibilities
	- Implementation
		- traditional methods of building software:
			- structured methodology - using data flow diagrams, stucture charts, process-oriented (data and process separation)
			- object-oriented development - combines data+behavior into objects, utilizing UML diagrams
		- alternative system building approaches:
			- prototyping (if there is some uncertainty about requirements or design solutions)
				- enables end-user involvement
			- end-user development (letting end-users to develop small apps using interactive platforms or AI and incorporating the knowledge to the final product)
			- outsourcing - hiring an external company to build it
				- drawbacks: hidden costs and losing control
			- application software packages - buying pre-written packages (SAP, Oracle, Salesforce)
				- drawbacks: changing the business processes to match the ones defined in the software packages
					- extended customization raises the costs exponentially
		- modern development approaches:
			- agile development - sprints, fast customer feedback, scrum, kanban
			- DevOps - integration of development and operations (CI/CD)
			- mobile-first development 
			- RAD (Rapid Application Development) - fast prototyping and reusable components
	- Testing
		- unit testing (testing programs separately)
		- systems testing (testing how modules function together)
		- acceptance testing (final certification for production)
	- Conversion
		- moving from the old system to the new one
		- different strategies:
			- parallel: both systems are running in parallel until the new is verified (safest, expensive)
			- direct cutover: switch at a fixed date (cheap, risky)
			- pilot study: try in one department and then roll out to the whole business at once
			- phased approach: introduce in stages one organizational unit at a time
	- Production
		- there could be a post-implementation audit included (see [[Excelentní procesy v ekonomické praxi]])
	- Maintenance
- the takeway: building an IS = managing an organizational change, the technical work of coding is just a fraction, the managers have to figure out the specific requirements, evaluate if the IS makes even sense, get people to use it and integrate it with existing systems and processes
# Integration of applications
- it is highly beneficial that the organization connects and integrates all specialized applications and systems together to enable data sharing across the enterprise
- manual integration is very time-consuming, fragile and highly prone to errors
### Integration styles
- file transfer - system A writes a file, system B reads it after, cheap integration
	- drawbacks: latency, file format issues, error handling is not present
- shared databases - data available fast, ACID consistent
	- drawbacks: schema changes break all systems, performance bottleneck
- remote procedure invocation ([[RPC protokol]]) - one system calls a function of the other system (REST, SOAP, gRPC)
- messaging - asynchronous message queues (RabbitMQ, Kafka...)
- all approaches trade simplicity for [[GRASP - Nízká provázanost|low coupling]]
### Data integration
- ETL (Extract-Transform-Load) - extracting from different sources and loading into a common shared data warehouse
- ELT (Extract-Load-Transform) - raw data are directly loaded into the goal system and transformed there (using the processing power of that system and reduce load times)
- MDM (Master Data Management) - maintain a single point of truth for critical enterprise data, maintain data quality
	- such as client roles, product supplier details, organization structures...
- terminology:
	- data lake: raw storage for structured and unstructured data
	- data warehouse: structured storage, optimized for analytics
	- data lakehouse: hybrid, combining lake flexibility with warehouse performance
	- data mesh: decentralized storage, domain-owned data instead of central database/warehouse
	- data mart: a highly focused subset of the data warehouse for a specific user group
### 3 levels of integration
- Integration on the UI level (mash-ups)
	- connecting different UI components with different data sources in a single web portal
	- composite applications that mix and match components together
	- e.g. Google Maps with real estate database to display local property listings on an interactive map
- Application interaction
	- relying on published APIs, feature calls and messaging between applications
	- SOAP/WSDL: older, strongly typed, heavyweight, used in enterprises and banks
	- REST: resource oriented, JSON, mainly for web
	- GraphQL: single endpoint, client specifies what it wants, solves over-fetching
	- gRPC: modern RPC over HTTP/2, used in microservices
	- Webhooks/events: pushing events instead of pulling
- ESB ([[Návrh architektury aplikace#Enterprise service bus]])
	- central broker for the centralized communication across the enterprise
		- does routing, transformation, protocol mediation, orchestration, monitoring
	- utilizing the SOA (Service Oriented Architecture)
		- it is an architectural pattern where application components provides services to other application components (over a network)
			- a service is a self-contained autonomous and loosely coupled unit of functionality (e.g. provide weather data, check customer credit...)
		- the principles are technology-agnostic, so it could be used for integrating legacy applications with the modern ones
		- the services are usually described by WSDL (Web Services Description Language)
		- SOAP (Simple Object Access Protocol) to describe the communication protocols
### Other notes on integration
- we have synchronous services (both systems need to be active at the same time) and asynchronous services (emitted messages are stored until picked up)
- web services are revolutionary in the systems integration as they provide components to exchange information using standard web protocols rather than some proprietary code
	- XML, SOAP, WSDL, UDDI (a mechanism to register and locate web service applications)
### Business problems of non-integration
- having information silos (for each department), with no shared views
- duplicated data entries (same data entered to the ERP, CRM and billing system independently)
- cross-functional business processes will not work (those are often the most important ones)
- high maintenance costs related to point-to-point integrations 
# Data management
- important distinction: data vs. information (see above)
- 6 layers of data:
	- metadata (data about data, data meaning, usage, data structures)
	- reference data (external tables of keys)
	- enterprise structure data (organization structures, processes, security, applications...)
	- transaction structure data (accounting books, catalogs, product catalogue, invoices...)
	- transaction activity data (data that are going to be processed into above structures)
	- transaction audit data (logs, audits)
### Data quality
- rule: garbage in, garbage out applies here for data
	- most issues/contaminations are at input (error data, wrong data, incorrect measurements, incomplete) and time (outdated data)
- continuous data cleansing is required to maintain high quality data
	- and also, companies could do internal data audits to control the quality of the data
- keeping high quality data is expensive, so do it only if demanded and necessary
- data quality dimensions:
	- accuracy - do the data describe the reality accurately?
	- completeness - are required values present?
	- consistency - are the values consistent across systems?
	- timeliness - are the data current enough to use?
	- validity - conform the data to rules/formats?
	- uniqueness - no unintended duplicates?
### Data consolidation
- collecting and integrating data from multiple sources to a single data store
	- ETL and ELT methods for this
- principle from [[Big Data]] are used to handle to large amounts of data effectively
### [[#Data integration]]
- already includes definition of MDM = Master Data Management
	- also connected to Data Administration and Data Governance (as the organization has to establish a formal Information Policy that involves all information about how the organization handles and stores data)
- metadata are important (it tells us how to interpret the data, how to work with it, increasing the informational value of data)
### Data normalization
- reducing data redundancy by breaking complex tables into a numerous smaller ones (minimizing the many-to-many relationships)
	- this is great for reducing the redundancy, but it creates another problem at query time (for each query, multiple joins over all tables must be performed, which is slow)
	- the solution: dimensional modelling
		- having one central "fact" table owning the ground truth and multiple "dimension" tables surrounding it (like time, product, region), which speed up the complex queries
		- yes, so some redundancy is kept in order to increase performance
### Data governance
- a control that ensures that the data entry (made by operations team member or an automated process) meets set standards:
	- as a business rule,
	- a data definition and 
	- data integrity constraints in the data model
- the data governor uses data quality monitoring (comparint against production data) and communicates errors in the data back to the operational team members
# E-Government
- it is an application of the Internet and networking technologies to digitally enable government and public sector to have relationships with citizens, businesses and other parts of the government
- easier, efficient and more transparent access to information and services + making the government operations much more efficient and convenient
### Implementation of E-Government
1. Start-up: one-way information flow, where government publishes static information on the web (laws, guidelines, handbooks etc.)
2. One-way interaction: citizens can download forms and send e-mails to the government, although, the government does not have to respond electronically
3. Two-way interaction: complete actions take place on the web (e.g. user obtaining a tax form online, filling it out and submitting it directly through the portal)
4. Transaction on the web (matured e-government), entire operations and transactions are made online
### 4 areas of services
- G2C = Government to Citizen
- G2B = Government to Business
- G2G = Government to Government
	- interaction within and between departments 
- G2E = Government to Employee