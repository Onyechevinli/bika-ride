---
title: "Deploying Node.js to Azure App-Service using Terraform."
datePublished: Tue Nov 11 2025 11:57:38 GMT+0000 (Coordinated Universal Time)
cuid: cmhuio8he000302l78fmh68m8
slug: deploying-nodejs-to-azure-app-service-using-terraform
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1762819342262/f98f4224-e969-49dd-9977-962e1c3cb38e.png
tags: github, azure, git, terraform, vscode-cjevho8kk00bo1ss2lmqqjr51, cicd-cjy1vtdk2005kjjs17n8couc3, github-actions-1

---

### To embark on this project, we should have VS Code, a GitHub account, an Azure account, and Azure CLI installed on a PC.

## Create a Simple Node.js Web App OPEN VS CODE.

📌 Create a new folder: NEBs

📌 Create the following file accordingly:

NEBs/

├── package.json

├── server.js

├── public/css/style.css

└── views

├──books.ejs

├── contact.ejs

|── economy.ejs

|── error.ejs

|── index.ejs

|── layout.ejs

### Below are the example codes:

package.json

```json


{
  "name": "nigerian-economy-books",
  "version": "1.0.0",
  "description": "Web app about Nigeria's economy and book sales",
  "main": "server.js",
  "scripts": {
    "start": "node server.js",
    "dev": "nodemon server.js"
  },
  "dependencies": {
    "body-parser": "^1.20.3",
    "ejs": "^3.1.10",
    "express": "^4.21.2",
    "express-ejs-layouts": "^2.5.1"
  },
  "devDependencies": {
    "nodemon": "^3.0.1"
  },
  "keywords": [
    "nigeria",
    "economy",
    "books",
    "express"
  ],
  "author": "Your Name",
  "license": "MIT"
}
```

server.js

```javascript

const express = require('express');
const bodyParser = require('body-parser');
const ejsLayouts = require('express-ejs-layouts');
const path = require('path');

const app = express();
const PORT = process.env.PORT || 5000;

// Sample book data
const BOOKS = [
    {
        id: 1,
        title: 'Understanding Nigeria\'s Economic Challenges',
        author: 'Dr. Chinedu Okoro',
        price: 4500,
        description: 'A comprehensive analysis of Nigeria\'s economic struggles and potential solutions.',
        image: 'book1.jpg'
    },
    {
        id: 2,
        title: 'The Path to Economic Recovery',
        author: 'Prof. Adeola Johnson',
        price: 5200,
        description: 'Practical strategies for navigating and overcoming economic hardships in Nigeria.',
        image: 'book2.jpg'
    },
    {
        id: 3,
        title: 'Entrepreneurship in Tough Times',
        author: 'Bola Ahmed',
        price: 3800,
        description: 'How to start and grow businesses despite economic challenges.',
        image: 'book3.jpg'
    },
    {
        id: 4,
        title: 'Financial Survival Guide',
        author: 'Chioma Eze',
        price: 4200,
        description: 'Personal finance management during economic instability.',
        image: 'book4.jpg'
    }
];

// Middleware
app.use(bodyParser.urlencoded({ extended: true }));
app.use(bodyParser.json());
app.use(express.static('public'));

// EJS setup
app.set('view engine', 'ejs');
app.set('views', path.join(__dirname, 'views'));
app.use(ejsLayouts);
app.set('layout', 'layout');

// Routes
app.get('/', (req, res) => {
    res.render('index', { 
        title: 'Nigeria Economic Insight - Books & Analysis by Vincent',
        activePage: 'home'
    });
});

app.get('/economy', (req, res) => {
    const economicIssues = [
        "High inflation rates affecting purchasing power",
        "Unemployment and underemployment challenges",
        "Naira depreciation and foreign exchange issues",
        "Infrastructure deficits",
        "Dependence on oil exports",
        "Rising cost of living",
        "Power supply challenges",
        "Security concerns affecting investments"
    ];
    
    const solutions = [
        "Diversification of the economy",
        "Investment in agriculture and manufacturing",
        "Improving infrastructure",
        "Encouraging local production",
        "Digital economy development",
        "Youth empowerment programs"
    ];
    
    res.render('economy', { 
        title: 'Economic Analysis - Nigeria',
        activePage: 'economy',
        issues: economicIssues,
        solutions: solutions
    });
});

app.get('/books', (req, res) => {
    res.render('books', { 
        title: 'Books - Economic Solutions',
        activePage: 'books',
        books: BOOKS
    });
});

app.get('/contact', (req, res) => {
    res.render('contact', { 
        title: 'Contact Us',
        activePage: 'contact',
        success: false,
        name: ''
    });
});

app.post('/contact', (req, res) => {
    const { name, email, message } = req.body;
    
    // In a real application, you would save this to a database
    console.log('Contact form submission:', { name, email, message });
    
    res.render('contact', { 
        title: 'Contact Us',
        activePage: 'contact',
        success: true,
        name: name
    });
});

// API Routes
app.get('/api/books', (req, res) => {
    res.json(BOOKS);
});

app.get('/api/books/:id', (req, res) => {
    const bookId = parseInt(req.params.id);
    const book = BOOKS.find(book => book.id === bookId);
    
    if (book) {
        res.json(book);
    } else {
        res.status(404).json({ error: 'Book not found' });
    }
});

// Error handling middleware
app.use((req, res) => {
    res.status(404).render('error', { 
        title: 'Page Not Found',
        message: 'The page you are looking for does not exist.'
    });
});

app.use((err, req, res, next) => {
    console.error(err.stack);
    res.status(500).render('error', { 
        title: 'Server Error',
        message: 'Something went wrong on our end. Please try again later.'
    });
});

app.listen(PORT, () => {
    console.log(`Server is running on http://localhost:${PORT}`);
    console.log(`Environment: ${process.env.NODE_ENV || 'development'}`);
});
```

public\\css\\style.css

```css
:root {
    --naija-green: #008753;
    --naija-white: #FFFFFF;
    --naija-yellow: #FFC72C;
}

.bg-green {
    background-color: var(--naija-green) !important;
}

.text-green {
    color: var(--naija-green) !important;
}

.btn-green {
    background-color: var(--naija-green);
    border-color: var(--naija-green);
    color: white;
}

.btn-green:hover {
    background-color: #006741;
    border-color: #006741;
    color: white;
}

.btn-outline-green {
    border-color: var(--naija-green);
    color: var(--naija-green);
}

.btn-outline-green:hover {
    background-color: var(--naija-green);
    color: white;
}

.hero-section {
    background: linear-gradient(135deg, var(--naija-green) 0%, #006741 100%);
    color: white;
    padding: 100px 0;
    min-height: 70vh;
    display: flex;
    align-items: center;
}

.min-vh-75 {
    min-height: 75vh;
}

.book-card {
    transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.book-card:hover {
    transform: translateY(-5px);
    box-shadow: 0 8px 25px rgba(0,0,0,0.15) !important;
}

.book-placeholder {
    height: 150px;
    display: flex;
    align-items: center;
    justify-content: center;
}

.navbar-brand {
    font-size: 1.5rem;
    font-weight: bold;
}

.card {
    border-radius: 10px;
}

.list-group-item {
    border: none;
    padding: 15px 20px;
}

.alert {
    border-radius: 10px;
}

.shadow {
    box-shadow: 0 4px 6px rgba(0,0,0,0.1) !important;
}

.nav-link.active {
    font-weight: bold;
    background-color: rgba(255, 255, 255, 0.1);
    border-radius: 5px;
}

@media (max-width: 768px) {
    .hero-section {
        padding: 60px 0;
        text-align: center;
    }
    
    .display-4 {
        font-size: 2.5rem;
    }
}
```

views\\books.ejs

```html
<div class="container mt-5">
    <h1 class="text-center text-green mb-5">Books on Economic Solutions</h1>
    
    <div class="row g-4">
        <% books.forEach(function(book) { %>
        <div class="col-md-6 col-lg-3">
            <div class="card h-100 shadow book-card">
                <div class="card-img-top bg-light text-center py-4">
                    <div class="book-placeholder">
                        <i class="fas fa-book fa-3x text-green"></i>
                    </div>
                </div>
                <div class="card-body d-flex flex-column">
                    <h5 class="card-title text-green"><%= book.title %></h5>
                    <p class="card-text text-muted">by <%= book.author %></p>
                    <p class="card-text flex-grow-1"><%= book.description %></p>
                    <div class="mt-auto">
                        <div class="d-flex justify-content-between align-items-center">
                            <span class="h5 text-success mb-0">₦<%= book.price.toLocaleString() %></span>
                            <button class="btn btn-warning" onclick="addToCart('<%= book.id %>')">
                                Add to Cart
                            </button>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <% }); %>
    </div>

    <div class="row mt-5">
        <div class="col-lg-8 mx-auto">
            <div class="card shadow">
                <div class="card-body text-center">
                    <h3 class="text-green">Why These Books Matter</h3>
                    <p class="lead">
                        Each book in our collection provides practical insights and strategies to help Nigerians navigate economic challenges, build resilience, and create opportunities for growth and prosperity.
                    </p>
                    <a href="/contact" class="btn btn-green btn-lg">
                        Contact for Bulk Orders
                    </a>
                </div>
            </div>
        </div>
    </div>
</div>

<script>
function addToCart(bookId) {
    alert('Book added to cart! In a real application, this would connect to a shopping cart system.');
    // Here you would typically make an API call to add the item to the cart
}
</script>
```

views\\contact.ejs

```html
<div class="container mt-5">
    <div class="row">
        <div class="col-lg-8 mx-auto">
            <h1 class="text-center text-green mb-5">Contact Us</h1>
            
            <% if (success) { %>
            <div class="alert alert-success alert-dismissible fade show" role="alert">
                <strong>Thank you, <%= name %>!</strong> Your message has been received. We'll get back to you soon.
                <button type="button" class="btn-close" data-bs-dismiss="alert"></button>
            </div>
            <% } %>

            <div class="card shadow">
                <div class="card-body p-4">
                    <form method="POST" action="/contact">
                        <div class="row g-3">
                            <div class="col-md-6">
                                <label for="name" class="form-label">Full Name</label>
                                <input type="text" class="form-control" id="name" name="name" required>
                            </div>
                            <div class="col-md-6">
                                <label for="email" class="form-label">Email Address</label>
                                <input type="email" class="form-control" id="email" name="email" required>
                            </div>
                            <div class="col-12">
                                <label for="message" class="form-label">Message</label>
                                <textarea class="form-control" id="message" name="message" rows="5" required></textarea>
                            </div>
                            <div class="col-12">
                                <button type="submit" class="btn btn-green btn-lg">Send Message</button>
                            </div>
                        </div>
                    </form>
                </div>
            </div>

            <div class="row mt-5">
                <div class="col-md-6">
                    <div class="card shadow">
                        <div class="card-body text-center">
                            <h5 class="text-green">Book Inquiries</h5>
                            <p class="mb-1">books@naijaeconomicinsight.com</p>
                            <p class="mb-0">+234 813 742 5765</p>
                        </div>
                    </div>
                </div>
                <div class="col-md-6">
                    <div class="card shadow">
                        <div class="card-body text-center">
                            <h5 class="text-green">General Inquiries</h5>
                            <p class="mb-1">info@naijaeconomicinsight.com</p>
                            <p class="mb-0">+234 907 078 2052</p>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
```

views\\economy.ejs

```html
<div class="container mt-5">
    <div class="row">
        <div class="col-lg-8 mx-auto">
            <h1 class="text-center text-green mb-5">Nigeria's Economic Challenges</h1>
            
            <div class="card shadow mb-4">
                <div class="card-header bg-green text-white">
                    <h3 class="mb-0">Current Economic Issues</h3>
                </div>
                <div class="card-body">
                    <ul class="list-group list-group-flush">
                        <% issues.forEach(function(issue) { %>
                        <li class="list-group-item">
                            <i class="fas fa-exclamation-circle text-warning me-2"></i>
                            <%= issue %>
                        </li>
                        <% }); %>
                    </ul>
                </div>
            </div>

            <div class="card shadow mb-4">
                <div class="card-header bg-success text-white">
                    <h3 class="mb-0">Potential Solutions</h3>
                </div>
                <div class="card-body">
                    <ul class="list-group list-group-flush">
                        <% solutions.forEach(function(solution) { %>
                        <li class="list-group-item">
                            <i class="fas fa-check-circle text-success me-2"></i>
                            <%= solution %>
                        </li>
                        <% }); %>
                    </ul>
                </div>
            </div>

            <div class="card shadow">
                <div class="card-body">
                    <h3 class="text-green">Detailed Analysis</h3>
                    <p>
                        Nigeria's economy faces multiple challenges including high inflation rates that have significantly reduced the purchasing power of citizens. The unemployment rate remains high, particularly among the youth population, leading to various social and economic consequences.
                    </p>
                    <p>
                        Nigeria, Africa's most populous nation and a country blessed with vast human and natural resources, stands at a critical juncture in its economic history. Despite its immense potential, the nation continues to grapple with persistent and multifaceted economic challenges that hinder its development and affect the livelihoods of millions. Addressing these issues is not merely an economic imperative but a social and political necessity to unlock the nation's promise.
                    </p>
                    <p>
                        <h4 class="text-green">Key Economic Issues in Nigeria</h4>
                    </p>
                    <p>
                        The current economic landscape in Nigeria is characterized by several deeply rooted structural and policy challenges:
                    </p>
                    <p>
                        Over-reliance on Oil: The economy remains heavily dependent on crude oil exports for government revenue and foreign exchange earnings, making it highly vulnerable to volatile global oil price fluctuations. This "resource curse" has crowded out growth in other vital sectors like agriculture and manufacturing.
                    </p>
                    <p>
                        High Inflation and Cost of Living: Nigeria faces a severe inflation and cost-of-living crisis, with inflation rates exceeding 20 percent and food inflation disproportionately impacting the most vulnerable households. Policy reforms, such as the removal of fuel subsidies and the liberalization of the foreign exchange market, while necessary long-term steps, have intensified short-term price pressures.
                    </p>
                    <p>
                        Inadequate Infrastructure and Power Supply: A significant deficit in basic infrastructure, particularly in the power sector, hampers economic activity and increases the cost of doing business. Many businesses rely on expensive alternative power sources, limiting industrial growth and productivity.
                    </p>
                    <p>
                        Unemployment and Poverty: High rates of unemployment and underemployment, especially among the youth, pose a significant socio-economic challenge. This situation breeds frustration and social unrest, contributing to increased crime rates and insecurity across the nation.
                    </p>
                    <p>
                        Governance Issues and Corruption: Widespread corruption, a lack of transparency, and policy inconsistency across successive administrations undermine investor confidence and hamper the effective use of public funds for national development. The current high debt-to-revenue ratio also places Nigeria among vulnerable countries in terms of debt sustainability, leaving little fiscal space for critical investments in infrastructure and social services.
                    </p>
                    <p>
                        Insecurity: A turbulent security situation involving insurgency, banditry, and ethnic conflicts disrupts economic activities, particularly agriculture in food-producing regions, contributing to food insecurity and displacement.
                    </p>
                    <p>
                        <h4 class="text-green">Solutions and the Path Forward</h4>
                    </p>
                    <p>
                        Addressing these challenges requires a comprehensive, strategic, and sustained approach involving both government and citizens.
                    </p>
                    <p>
                        Economic Diversification: The cornerstone of recovery must be a decisive shift away from oil dependency. This involves providing incentives for non-oil exports and investing heavily in sectors with high growth potential, such as agriculture, technology, and services. Supporting local industries and patronizing locally made goods can also reduce import dependence.
                    </p>
                    <p>
                        Macroeconomic Stability and Fiscal Discipline: The government needs to maintain a tight monetary policy to bring inflation under control and ensure the exchange rate reflects market conditions. Strengthening the domestic revenue collection base through effective tax reforms and cutting down on wasteful expenditure are essential to improve fiscal stability and reduce the debt burden.
                    </p>
                    <p>
                        Infrastructure Development: Significant, transparent investment in critical infrastructure, particularly power, transport, and digital connectivity, is essential to lower business costs and boost productivity. Encouraging private sector participation in infrastructure projects can help bridge the funding gap.
                    </p>
                    <p>
                        Human Capital Development and Job Creation: Prioritizing investment in quality education, health, and skills training can unlock the potential of Nigeria's large youth population. Creating an enabling environment for Small and Medium Enterprises (SMEs) and startups is crucial for job creation.
                    </p>
                    <p>
                        Strengthening Governance and Institutions: Implementing stronger anti-corruption measures, enforcing transparency laws, and empowering the judiciary to prosecute offenders without interference are vital steps. Building strong, accountable institutions is fundamental to fostering trust and attracting both domestic and foreign investment.
                    </p>
                    <p>
                        Social Safety Nets: To cushion the impact of ongoing reforms and economic shocks on the most vulnerable, it is critical to scale up and institutionalize targeted social safety net programs, such as cash transfers.
                    </p>
                    <p>
                        <h4 class="text-green">Conclusion</h4>
                    </p>
                    <p>
                        The dependence on oil exports makes the economy vulnerable to global oil price fluctuations. Infrastructure deficits, particularly in power supply and transportation, increase the cost of doing business and reduce competitiveness.
                    </p>
                    <p>
                        However, there is hope through economic diversification, investment in human capital, and leveraging technology to create new opportunities for growth and development.
                    </p>
                </div>
            </div>

            <div class="text-center mt-4">
                <a href="/books" class="btn btn-warning btn-lg">
                    Explore Solution Books
                </a>
            </div>
        </div>
    </div>
</div>
```

views\\error.ejs

```html
<div class="container mt-5">
    <div class="row">
        <div class="col-lg-6 mx-auto text-center">
            <div class="card shadow">
                <div class="card-body py-5">
                    <h1 class="text-green mb-4">Oops! it's not a deadend, just go back!</h1>
                    <p class="lead mb-4"><%= message %></p>
                    <a href="/" class="btn btn-green btn-lg">Go Home</a>
                </div>
            </div>
        </div>
    </div>
</div>
```

views\\index.ejs

```html
<div class="hero-section">
    <div class="container">
        <div class="row align-items-center min-vh-75">
            <div class="col-lg-6">
                <h1 class="display-4 fw-bold text-white mb-4">
                    Understanding Nigeria's Economic Journey
                </h1>
                <p class="lead text-white mb-4">
                    Explore in-depth analysis of Nigeria's economic challenges and discover books that provide practical solutions for individuals and businesses.
                </p>
                <div class="d-grid gap-2 d-md-flex justify-content-md-start">
                    <a href="/economy" class="btn btn-warning btn-lg px-4 me-md-2">
                        Learn About Economy
                    </a>
                    <a href="/books" class="btn btn-outline-light btn-lg px-4">
                        Browse Books
                    </a>
                </div>
            </div>
        </div>
    </div>
</div>

<div class="container my-5">
    <div class="row g-4">
        <div class="col-md-4">
            <div class="card h-100 border-0 shadow">
                <div class="card-body text-center">
                    <h3 class="card-title text-green">Economic Analysis</h3>
                    <p class="card-text">Comprehensive breakdown of Nigeria's economic challenges and their impact on citizens.</p>
                    <a href="/economy" class="btn btn-outline-green">Explore Analysis</a>
                </div>
            </div>
        </div>
        <div class="col-md-4">
            <div class="card h-100 border-0 shadow">
                <div class="card-body text-center">
                    <h3 class="card-title text-green">Solution Books</h3>
                    <p class="card-text">Curated books offering practical solutions for navigating economic hardships.</p>
                    <a href="/books" class="btn btn-outline-green">View Books</a>
                </div>
            </div>
        </div>
        <div class="col-md-4">
            <div class="card h-100 border-0 shadow">
                <div class="card-body text-center">
                    <h3 class="card-title text-green">Community</h3>
                    <p class="card-text">Join the conversation and share your experiences and solutions.</p>
                    <a href="/contact" class="btn btn-outline-green">Get Involved</a>
                </div>
            </div>
        </div>
    </div>
</div>

<div class="bg-light py-5">
    <div class="container">
        <div class="row">
            <div class="col-lg-8 mx-auto text-center">
                <h2 class="text-green mb-4">Why This Matters</h2>
                <p class="lead">
                    Nigeria's economic challenges affect millions of citizens. Understanding these issues is the first step toward finding sustainable solutions and building a better future for all Nigerians.
                </p>
            </div>
        </div>
    </div>
</div>
```

views\\layout.ejs

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title><%= title || 'Nigeria Economic Insight - Books & Analysis by Vincent' %></title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" rel="stylesheet">
    <link rel="stylesheet" href="/css/style.css">
</head>
<body>
    <nav class="navbar navbar-expand-lg navbar-dark bg-green">
        <div class="container">
            <a class="navbar-brand" href="/">
                <strong>NaijaEconomicInsight</strong>
            </a>
            <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav">
                <span class="navbar-toggler-icon"></span>
            </button>
            <div class="collapse navbar-collapse" id="navbarNav">
                <ul class="navbar-nav ms-auto">
                    <li class="nav-item">
                        <a class="nav-link <%= activePage === 'home' ? 'active' : '' %>" href="/">Home</a>
                    </li>
                    <li class="nav-item">
                        <a class="nav-link <%= activePage === 'economy' ? 'active' : '' %>" href="/economy">Economic Analysis</a>
                    </li>
                    <li class="nav-item">
                        <a class="nav-link <%= activePage === 'books' ? 'active' : '' %>" href="/books">Books</a>
                    </li>
                    <li class="nav-item">
                        <a class="nav-link <%= activePage === 'contact' ? 'active' : '' %>" href="/contact">Contact</a>
                    </li>
                </ul>
            </div>
        </div>
    </nav>

    <main>
        <%- body %>
    </main>

    <footer class="bg-dark text-white mt-5">
        <div class="container py-4">
            <div class="row">
                <div class="col-md-6">
                    <h5>NaijaEconomicInsight</h5>
                    <p>Providing analysis and solutions for Nigeria's economic challenges.</p>
                </div>
                <div class="col-md-6 text-md-end">
                    <p>&copy; 2024 NaijaEconomicInsight. All rights reserved.</p>
                </div>
            </div>
        </div>
    </footer>

    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

You can generate your own Node.js file to your discretion and arrange it accordingly in the VS Code app. Please always save your work for every edit, so that when running any code, it will capture your latest edit.

---

## Running the web-app locally.

This is optional to test your web-app locally, but I’ll give you the steps to know or get a visualization of what your web-app will look like.

Run these commands:

`npm init` `npm install express ejs`

Test locally, run: `node server.js`

Open your web browser and run this URL → http://localhost:5000

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1762824577576/44532c4f-5269-4cef-b424-e2a7e00395c5.png align="center")

---

## PUSHING THE PROJECT TO GITHUB.

On your VS Code, go to the 3 dots beside the FILE tab and click on TERMINAL, select NEW TERMINAL, then on the terminal, run the following commands:

🧩 STEP 1 — Initialize Git in your Project Folder In VS Code’s terminal (inside your project root folder, e.g., NEBs): `git init` This creates a local .git repository.

🧩 STEP 2 — Add Your Files to Git `git add .` That stages all files in your folder for commit.

🧩 STEP 3 — Commit the Files `git commit -m "first commit"` This saves the current version to your local repo.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1762841808612/c0897a2a-e54c-4cd3-9f10-029fc16ec8a2.png align="center")

🧩 STEP 4 — Create a GitHub Repository. Go to [https://github.com/new](https://github.com/new). Give it a name, e.g., HEBs Choose Public. Do not initialize with a README (since you already have files locally). Click Create Repository. GitHub will show you a page with instructions to push existing code — keep that open.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1762842178662/a0a23b0c-6d9a-4068-ade4-5fe6b41bf08b.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1762842283921/50550562-72f5-4c15-91e2-70dc1266e998.png align="center")

🧩 STEP 5 — Connect Your Local Repo to GitHub from the VS Code terminal, copy and run the commands GitHub shows, or manually: `git remote add origin https://github.com/<your-username>/<repo-name>.git` Example: `git remote add origin https://github.com/onyechevinli/HEBs.git`

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1762844865351/6aa7f045-9002-4522-a8be-71479a82bcdf.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1762844989693/6cd579cb-e39c-4ff2-b0db-1c91c3535905.png align="center")

🧩 STEP 6 — Push to GitHub `git branch -M main` then run, `git push -u origin main` # if you encounter error like <mark>"error: failed to push some refs to ..."</mark>, run: `git pull --rebase origin main` then run, `git push -u origin main` If prompted, sign in to GitHub or paste a Personal Access Token (PAT) instead of your password. 🔒 (GitHub no longer accepts passwords over HTTPS).

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1762845160650/47144395-ad61-4fc3-a264-cba8083664e8.png align="center")

🧩 STEP 7 — Verify. Go to your GitHub repository URL: https://github.com/&lt;your-username&gt;/&lt;repo-name&gt; 🎉 You should now see all your VS Code files online. ✅ Future updates: When you make new changes later: `git add .` , `git commit -m "Updated Node.js app"` , `git push`

---

## Building the Infrastructure as code using Terraform.

We create a Terraform folder in the root directory and create the following files accordingly:

NEBs/terraform/

├── main.tf

├── outputs.tf

├── variables.tf

└── terraform.tfvars

### Below is the Terraform code:

main.tf

```json

# Configure the Azure Provider
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~>3.0"
    }
  }
}

# Configure the Microsoft Azure Provider
provider "azurerm" {
  features {}
}

# Create a resource group
resource "azurerm_resource_group" "main" {
  name     = var.resource_group_name
  location = var.location

  tags = {
    Environment = "Development"
    Project     = "AppService Demo"
  }
}

# Create an App Service Plan
resource "azurerm_service_plan" "main" {
  name                = var.ASP
  resource_group_name = azurerm_resource_group.main.name
  location            = azurerm_resource_group.main.location
  os_type             = "Linux"
  sku_name            = var.sku_name



  tags = {
    Environment = "Development"
    Project     = "AppService Demo"
  }
}

# Create the App Service
resource "azurerm_linux_web_app" "main" {
  name                = "${var.web_app_name}-${random_string.suffix.result}"
  resource_group_name = azurerm_resource_group.main.name
  location            = azurerm_service_plan.main.location
  service_plan_id     = azurerm_service_plan.main.id

  site_config {
    application_stack {
      # Example: Node.js application
      node_version = var.node_V
    }
    always_on = false # Required for free tier
  }

  app_settings = {
    "WEBSITE_NODE_DEFAULT_VERSION" = "~20"
    "SCM_DO_BUILD_DURING_DEPLOYMENT" = "true"
  }

  tags = {
    Environment = "Development"
    Project     = var.project_name
  }
}

# Generate a random suffix for unique naming
resource "random_string" "suffix" {
  length  = 6
  special = false
  upper   = false
}
```

Outputs.tf

```json
output "resource_group_name" {
  description = "Name of the created resource group"
  value       = azurerm_resource_group.main.name
}

output "app_service_plan_name" {
  description = "Name of the created App Service Plan"
  value       = azurerm_service_plan.main.name
}

output "web_app_name" {
  description = "Name of the created Web App"
  value       = azurerm_linux_web_app.main.name
}

output "web_app_url" {
  description = "URL of the deployed web application"
  value       = "https://${azurerm_linux_web_app.main.default_hostname}"
}

output "web_app_default_hostname" {
  description = "Default hostname of the web application"
  value       = azurerm_linux_web_app.main.default_hostname
}

output "app_service_plan_id" {
  description = "ID of the App Service Plan"
  value       = azurerm_service_plan.main.id
}

output "resource_group_location" {
  description = "Location of the resource group"
  value       = azurerm_resource_group.main.location
}
```

variables.tf

```json
variable "resource_group_name" {
  description = "Name of the resource group"
  type        = string
  default     = "NEB-RG"
}

variable "location" {
  description = "Azure region for resources"
  type        = string
  default     = "canada central"
}

variable "ASP" {
  description = "Name of the App Service Plan"
  type        = string
  default     = "NEB-asp"
}

variable "sku_name" {
  description = "SKU for the App Service Plan"
  type        = string
  default     = "F1"

  validation {
    condition = can(regex("^(F1|D1|B1|B2|B3|S1|S2|S3|P1|P2|P3|P1v2|P2v2|P3v2|P1v3|P2v3|P3v3)$", var.sku_name))
    error_message = "The sku_name must be a valid Azure App Service Plan SKU."
  }
}

variable "web_app_name" {
  description = "Name of the web app (will have random suffix added)"
  type        = string
  default     = "NEB-webapp"
}

variable "node_V" {
  description = "Node.js version for the application stack"
  type        = string
  default     = "20-lts"
}

variable "environment" {
  description = "Environment name for tagging"
  type        = string
  default     = "Development"
}

variable "project_name" {
  description = "Project name for tagging"
  type        = string
  default     = "NEB-Application"
}
```

terraform.tfvars

```json
# Example Terraform variables file
# Copy this to terraform.tfvars and modify as needed

resource_group_name     = "NEB-RG"
location                = "canada central"
ASP                     = "NEB-asp"
sku_name                = "B1"
web_app_name            = "NEB-webapp"
node_V                  = "20-lts"
environment             = "Production"
project_name            = "NEB-Application"
```

### To deploy this infrastructure:

Set up Azure authentication (add Azure secrets to your workspace)

run the following commands in the Terraform folder:

`az login`

A tab would pop up on the screen, prompting you to sign in or select your preferred account, then choose the appropriate subscription.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1762846341864/fe2110e3-d65d-4300-86ef-5a0c34e9d910.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1762846357315/2815ec56-4b5f-4ec1-bd2e-de88bacc2d47.png align="center")

### Then run the following Terraform commands:

Initialize: `terraform init`

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1762847071014/2050c2ba-6c4a-4b69-85a9-489cef13ea96.png align="center")

Plan: `terraform plan`

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1762847468018/1e89ecfc-e4f1-4aea-8575-64d11abbdf7d.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1762847585958/89efd522-451c-4dbc-bb26-efa62bc092a8.png align="center")

Deploy: `terraform apply`

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1762847738466/e453fe05-cd1d-4a45-a91e-051fbccf19d7.png align="center")

You would be prompted to perform the ‘apply’ action, type in “yes”

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1762847828856/2afe7960-56de-458e-b706-d8f75f51367c.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1762847847920/2dfeb2aa-f297-4b47-b993-81eb28eaf75d.png align="center")

---

## After Deployment.

You can access the web app using the URL provided in the Terraform output, but you must first connect the web app to the code repository (GitHub) for continuous deployment by following these steps:

1. Go to the Azure Portal ([https://portal.azure.com](https://portal.azure.com)) and sign in to your Azure account, select Resource group.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1762849172118/fc76adc0-83aa-4c07-a03b-adb030f32d34.png align="center")

2. Navigate to your newly created Web App (NEB-RG).
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1762849311084/f3c2ea55-995a-41d8-a98f-74b4b9972554.png align="center")

3. Select the App Service (NEB-webapp-f2zf7|).
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1762849346515/823a4e65-b7f8-457e-9066-ba39e64502dd.png align="center")

4. In the left-hand menu, under "Deployment," click on "Deployment Center."
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1762858199872/88e67fea-9829-4e8f-bf21-c8d51fa523e3.png align="center")

5. In the Deployment center, select the source, which is where you are deploying the code from.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1762858331444/4ea79d58-b3fa-4238-9348-baafbcc5a409.png align="center")

6. Choose "GitHub" as the source and click "Continue."
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1762859134175/4119cf17-47aa-43de-a392-5db45a84d99b.png align="center")

Authenticate with your GitHub account if prompted.

7. Select the organization, repository, and branch where your Node.js web app code is hosted.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1762849787462/2ee9b498-38aa-44dc-ada3-f6eedb823812.png align="center")

* 8. Alos select ‘add a workflow, to create a workflow ymal file in your repo or you can select use available workflow, if you have created one already in the repo.
        

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1762849899307/05a91e4e-3bd1-4b2f-9fc1-f5d558c215a7.png align="center")

9. Review the settings and click "save" to set up the continuous deployment.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1762849952609/da85a8bb-ab8b-4df4-944d-aeb751aaa54f.png align="center")

10. Azure will now automatically pull the latest code from your GitHub repository and deploy it to your Web App.
    
11. Once the deployment is complete, you can access your Node.js web app using the URL (Domain name) provided in the Azure Portal for your Web App.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1762857700908/e419237b-3645-45c4-aea6-4a585b4ad5a4.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1762857849048/1a188eb6-de39-4311-a529-6fea7112ded0.png align="center")

\# after deployment of linking the app service to the github repository and you encounter any issue with the deployment you can always check the logs by navigating to the "Log Stream" section under "Monitoring" in your Web App's settings in the Azure Portal. This will provide real-time logs that can help you identify and troubleshoot any issues with your Node.js application deployment. or you can copy the error message and search online (AI tools) for possible solutions.

---

## Project Structure

├── .github

│ └── workflows

│ └── node.js.yml

├── NEBs

│ ├── app.js

│ ├── package.json

│ └── views

│ ├── contact.ejs

│ ├── economy.ejs

│ ├── error.ejs

│ ├── index.ejs

│ ├── layout.ejs

└── terraform

├─ [main.tf](http://main.tf)

├─ [outputs.tf](http://outputs.tf)

├─ [variables.tf](http://variables.tf)

└─ terraform.tfvars