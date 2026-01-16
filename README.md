# Habitfy
HABITFY - A Premium, Privacy-First, Gamified Experience.
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Dashboard - Habitfy</title>
    <link rel="stylesheet" href="style.css">
    <!-- Lucide Icons -->
    <script src="https://unpkg.com/lucide@latest"></script>
    <script src="utils.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        .dashboard-container {
            display: flex;
            min-height: 100vh;
        }

        .sidebar {
            width: 250px;
            background: var(--bg-secondary);
            border-right: 1px solid var(--glass-border);
            padding: var(--spacing-md);
            display: flex;
            flex-direction: column;
            position: fixed;
            height: 100vh;
        }

        .main-content {
            margin-left: 250px;
            flex: 1;
            padding: var(--spacing-lg);
        }

        .nav-item {
            display: flex;
            align-items: center;
            gap: 10px;
            padding: 10px 15px;
            color: var(--text-secondary);
            border-radius: var(--radius-sm);
            margin-bottom: 5px;
            transition: all 0.3s;
            cursor: pointer;
        }

        .nav-item:hover,
        .nav-item.active {
            background: rgba(124, 58, 237, 0.1);
            color: var(--accent-primary);
        }

        .section-title {
            margin-top: var(--spacing-md);
            margin-bottom: var(--spacing-sm);
            font-size: 1.2rem;
            color: var(--text-secondary);
            text-transform: uppercase;
            letter-spacing: 1px;
        }

        .habit-card {
            background: var(--glass-bg);
            border: 1px solid var(--glass-border);
            padding: var(--spacing-sm);
            border-radius: var(--radius-sm);
            margin-bottom: 10px;
            display: flex;
            align-items: center;
            justify-content: space-between;
            transition: transform 0.2s;
        }

        .habit-card:hover {
            transform: translateX(5px);
            border-color: var(--accent-primary);
        }

        .habit-checkbox {
            width: 24px;
            height: 24px;
            border-radius: 6px;
            border: 2px solid var(--text-secondary);
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            transition: all 0.2s;
        }

        .habit-checkbox.checked {
            background: var(--success);
            border-color: var(--success);
        }

        /* Modal */
        .modal-overlay {
            position: fixed;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: rgba(0, 0, 0, 0.7);
            backdrop-filter: blur(5px);
            display: none;
            justify-content: center;
            align-items: center;
            z-index: 1000;
        }

        .modal-overlay.active {
            display: flex;
        }

        .fab {
            position: fixed;
            bottom: 30px;
            right: 30px;
            width: 60px;
            height: 60px;
            border-radius: 50%;
            background: var(--accent-primary);
            color: white;
            display: flex;
            align-items: center;
            justify-content: center;
            box-shadow: 0 4px 15px var(--accent-glow);
            cursor: pointer;
            border: none;
            font-size: 24px;
            transition: transform 0.3s;
        }

        .fab:hover {
            transform: scale(1.1) rotate(90deg);
        }
    </style>
</head>

<body>
    <div class="dashboard-container">
        <!-- Sidebar -->
        <aside class="sidebar">
            <h2 class="text-accent mb-4">Habitfy</h2>

            <!-- Level Display -->
            <div class="glass-card mb-4"
                style="padding: 10px; background: rgba(124, 58, 237, 0.1); border: 1px solid var(--accent-primary);">
                <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 5px;">
                    <span style="font-weight: bold; color: var(--accent-primary);">Level <span
                            id="userLevel">1</span></span>
                    <span class="text-sm" id="userXP">0 XP</span>
                </div>
                <div style="width: 100%; height: 6px; background: rgba(255,255,255,0.1); border-radius: 3px;">
                    <div id="xpBar"
                        style="width: 0%; height: 100%; background: var(--accent-primary); border-radius: 3px; transition: width 0.5s;">
                    </div>
                </div>
            </div>

            <nav style="flex: 1;">
                <div class="nav-item active" onclick="app.navigate('habits')">
                    <i data-lucide="check-square"></i> Habits
                </div>
                <div class="nav-item" onclick="app.navigate('stats')">
                    <i data-lucide="bar-chart-2"></i> Statistics
                </div>
                <div class="nav-item" onclick="app.navigate('history')">
                    <i data-lucide="calendar"></i> History
                </div>
                <div class="nav-item" onclick="app.navigate('leaderboard')">
                    <i data-lucide="trophy"></i> Leaderboard
                </div>
                <div class="nav-item" onclick="app.navigate('store')">
                    <i data-lucide="shopping-bag"></i> Rewards Store
                </div>
            </nav>

            <div style="border-top: 1px solid var(--glass-border); padding-top: 10px; margin-top: 10px;">
                <div class="nav-item" onclick="app.toggleTheme()">
                    <i data-lucide="sun"></i> Theme
                </div>
                <div class="nav-item" onclick="Utils.Data.export()">
                    <i data-lucide="download"></i> Export Data
                </div>
                <div class="nav-item" onclick="Utils.Auth.logout()">
                    <i data-lucide="log-out"></i> Logout
                </div>
            </div>
        </aside>

        <!-- Main Content -->
        <main class="main-content">
            <header style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 30px;">
                <div>
                    <h1 id="greeting">Hello, User</h1>
                    <p style="color: var(--text-secondary);" id="dateDisplay">Today is...</p>
                </div>
                <!-- Profile or other header items -->
            </header>

            <!-- Views -->
            <div id="view-habits">
                <!-- Daily Summary Card -->
                <div id="daily-summary" class="glass-card mb-4"
                    style="background: linear-gradient(135deg, rgba(124, 58, 237, 0.4), rgba(45, 212, 191, 0.2));">
                    <div style="display: flex; align-items: center; justify-content: space-between;">
                        <div>
                            <h2>Today's Focus</h2>
                            <p id="summary-text">You have completed 0/0 habits today.</p>
                        </div>
                        <div class="text-accent" style="font-size: 40px; font-weight: bold;" id="summary-percentage">0%
                        </div>
                    </div>
                    <!-- Detailed Habit Status List -->
                    <div id="summary-habits-list"
                        style="margin-top: 15px; border-top: 1px solid rgba(255,255,255,0.1); padding-top: 10px; display: grid; grid-template-columns: repeat(auto-fill, minmax(150px, 1fr)); gap: 10px;">
                    </div>
                </div>

                <div id="habits-container">
                    <!-- Populated by JS -->
                </div>
            </div>

            <div id="view-profile" style="display: none;">
                <h2>My Profile</h2>
                <div class="glass-card" id="profile-container" style="text-align: center; padding: 40px;">
                    <!-- Populated by JS -->
                </div>
            </div>

            <div id="view-stats" style="display: none;">
                <h2>Statistics</h2>
                <!-- Key Metrics Row -->
                <div class="glass-card mb-4" id="stats-metrics">
                    <!-- Populated by JS -->
                </div>

                <!-- Charts Row -->
                <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 20px;">
                    <div class="glass-card">
                        <h3>Habit Distribution</h3>
                        <canvas id="pieChart"></canvas>
                    </div>
                    <div class="glass-card">
                        <h3>Weekly Consistency</h3>
                        <canvas id="barChart"></canvas>
                    </div>
                </div>
            </div>

            <div id="view-leaderboard" style="display: none;">
                <h2>Leaderboard</h2>
                <div class="glass-card" id="leaderboard-container">
                    <!-- Populated by JS -->
                </div>
            </div>

            <div id="view-store" style="display: none;">
                <h2>Rewards Store</h2>
                <div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(250px, 1fr)); gap: 20px;"
                    id="store-container">
                    <!-- Store Items -->
                </div>

                <h3 class="section-title">My Coupons</h3>
                <div class="glass-card" id="my-coupons-container">
                    <p style="color: var(--text-secondary);">No coupons redeemed yet.</p>
                </div>
            </div>

            <div id="view-history" style="display: none;">
                <h2>History</h2>
                <div class="glass-card">
                    <p>History log coming soon...</p>
                </div>
            </div>
        </main>
    </div>

    <!-- Add Habit Modal -->
    <div class="modal-overlay" id="addHabitModal">
        <div class="glass-card" style="width: 100%; max-width: 500px;">
            <div style="display: flex; justify-content: space-between; margin-bottom: 20px;">
                <h2>New Habit</h2>
                <button class="btn-outline" style="border:none; padding: 5px;"
                    onclick="app.toggleModal(false)">✕</button>
            </div>

            <form id="addHabitForm">
                <div class="input-group">
                    <label>Habit Name</label>
                    <input type="text" id="habitTitle" class="input-field" required placeholder="e.g. Read 30 mins">
                </div>

                <div class="input-group">
                    <label>Type</label>
                    <select id="habitType" class="input-field" style="color: white;">
                        <option value="daily">Daily</option>
                        <option value="periodic">Periodic (e.g. Weekly)</option>
                        <option value="single">One-time Task</option>
                    </select>
                </div>

                <div class="input-group" id="periodicOptions" style="display: none;">
                    <label>Target per week</label>
                    <input type="number" id="habitFrequency" class="input-field" min="1" max="7" value="3">
                </div>

                <div class="input-group">
                    <label>Daily Reminder (Optional)</label>
                    <input type="time" id="habitReminder" class="input-field" style="color-scheme: dark;">
                </div>

                <button type="submit" class="btn btn-primary w-full mt-4">Create Habit</button>
            </form>
        </div>
    </div>

    <!-- Floating Action Button -->
    <button class="fab" onclick="app.toggleModal(true)">+</button>

    <script src="dashboard.js"></script>
    <script>
        lucide.createIcons();
    </script>
</body>

</html>
