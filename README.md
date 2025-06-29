# Deep-Focus
Learning React by building a Pomodoro timer App.
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>FocusFlow - Fixed Pomodoro Timer</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <script>
        tailwind.config = {
            darkMode: 'class',
            theme: {
                extend: {
                    colors: {
                        'pomodoro-red': '#e74c3c',
                        'pomodoro-green': '#2ecc71',
                        'pomodoro-blue': '#3498db'
                    },
                    animation: {
                        'fade-in': 'fadeIn 0.3s ease-in-out',
                        'slide-up': 'slideUp 0.3s ease-out'
                    },
                    keyframes: {
                        fadeIn: {
                            '0%': { opacity: 0 },
                            '100%': { opacity: 1 }
                        },
                        slideUp: {
                            '0%': { transform: 'translateY(20px)', opacity: 0 },
                            '100%': { transform: 'translateY(0)', opacity: 1 }
                        }
                    }
                }
            }
        }
    </script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap');
        
        body {
            font-family: 'Inter', sans-serif;
            transition: background-color 0.3s, color 0.3s;
        }
        
        .tab-content {
            display: none;
            animation: fade-in 0.3s ease-in-out;
        }
        
        .tab-content.active {
            display: block;
        }
        
        .progress-ring__circle {
            transition: stroke-dashoffset 0.35s;
            transform: rotate(-90deg);
            transform-origin: 50% 50%;
        }
        
        .modal {
            transition: opacity 0.3s ease, transform 0.3s ease;
        }
        
        .modal-hidden {
            opacity: 0;
            transform: translateY(20px);
            pointer-events: none;
        }
        
        .session-item:hover, .task-item:hover {
            transform: translateY(-2px);
            box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1);
        }
        
        @keyframes pulse {
            0% { transform: scale(1); }
            50% { transform: scale(1.05); }
            100% { transform: scale(1); }
        }
        
        .pulse {
            animation: pulse 2s infinite;
        }
        
        .dark .nav-link.active {
            border-color: #f8fafc;
        }
    </style>
</head>
<body class="min-h-screen bg-gray-50 dark:bg-gray-900">
    <!-- Navigation -->
    <nav class="bg-white dark:bg-gray-800 shadow-sm">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
            <div class="flex justify-between h-16">
                <div class="flex items-center">
                    <div class="flex-shrink-0 flex items-center">
                        <i class="fas fa-clock text-indigo-600 dark:text-indigo-400 text-2xl mr-2"></i>
                        <span class="text-xl font-bold text-gray-900 dark:text-white">FocusFlow</span>
                    </div>
                </div>
                <div class="hidden md:ml-6 md:flex md:items-center md:space-x-8">
                    <a href="#timer" class="nav-link active text-gray-900 dark:text-white inline-flex items-center px-1 pt-1 border-b-2 border-indigo-500 text-sm font-medium" data-tab="timer">
                        Timer
                    </a>
                    <a href="#progress" class="nav-link text-gray-500 hover:text-gray-700 dark:text-gray-300 dark:hover:text-gray-100 inline-flex items-center px-1 pt-1 border-b-2 border-transparent hover:border-gray-300 text-sm font-medium" data-tab="progress">
                        Progress
                    </a>
                </div>
                <div class="flex items-center">
                    <button id="darkModeToggle" class="p-2 rounded-full hover:bg-gray-100 dark:hover:bg-gray-700 mr-3">
                        <i id="darkModeIcon" class="fas fa-moon text-gray-600 dark:text-yellow-300"></i>
                    </button>
                    <button id="settingsBtn" class="p-2 rounded-full hover:bg-gray-100 dark:hover:bg-gray-700">
                        <i class="fas fa-cog text-gray-500 dark:text-gray-300"></i>
                    </button>
                </div>
            </div>
        </div>
    </nav>

    <!-- Main Content -->
    <main class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
        <!-- Timer Tab -->
        <div id="timer-tab" class="tab-content active">
            <div class="flex flex-col md:flex-row gap-8">
                <!-- Timer Section -->
                <div class="w-full md:w-2/3 bg-white dark:bg-gray-800 rounded-xl shadow-sm p-6">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-2xl font-bold text-gray-900 dark:text-white">Pomodoro Timer</h2>
                        <div class="flex space-x-2">
                            <button id="statsBtn" class="p-2 rounded-full hover:bg-gray-100 dark:hover:bg-gray-700">
                                <i class="fas fa-chart-line text-gray-500 dark:text-gray-300"></i>
                            </button>
                        </div>
                    </div>
                    
                    <!-- Timer Modes -->
                    <div class="flex justify-center mb-8">
                        <div class="inline-flex rounded-md shadow-sm" role="group">
                            <button type="button" data-mode="pomodoro" class="mode-btn px-4 py-2 text-sm font-medium rounded-l-lg border border-gray-200 dark:border-gray-600 bg-indigo-600 text-white">
                                Focus
                            </button>
                            <button type="button" data-mode="shortBreak" class="mode-btn px-4 py-2 text-sm font-medium border-t border-b border-gray-200 dark:border-gray-600 bg-white dark:bg-gray-700 text-gray-700 dark:text-white hover:bg-gray-50 dark:hover:bg-gray-600">
                                Short Break
                            </button>
                            <button type="button" data-mode="longBreak" class="mode-btn px-4 py-2 text-sm font-medium rounded-r-lg border border-gray-200 dark:border-gray-600 bg-white dark:bg-gray-700 text-gray-700 dark:text-white hover:bg-gray-50 dark:hover:bg-gray-600">
                                Long Break
                            </button>
                        </div>
                    </div>
                    
                    <!-- Circular Timer -->
                    <div class="flex justify-center mb-8">
                        <div class="relative w-64 h-64">
                            <svg class="w-full h-full" viewBox="0 0 100 100">
                                <circle class="text-gray-200 dark:text-gray-700" stroke-width="8" stroke="currentColor" fill="transparent" r="40" cx="50" cy="50" />
                                <circle id="progress-circle" class="progress-ring__circle text-indigo-600" stroke-width="8" stroke-linecap="round" stroke="currentColor" fill="transparent" r="40" cx="50" cy="50" />
                            </svg>
                            <div class="absolute inset-0 flex flex-col items-center justify-center">
                                <span id="time-display" class="text-4xl font-bold text-gray-800 dark:text-white">50:00</span>
                                <span id="timer-label" class="text-lg text-gray-500 dark:text-gray-300 mt-2">Focus Time</span>
                            </div>
                        </div>
                    </div>
                    
                    <!-- Timer Controls -->
                    <div class="flex justify-center space-x-4 timer-controls">
                        <button id="startBtn" class="px-6 py-3 bg-indigo-600 text-white font-medium rounded-lg shadow-sm hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500">
                            <i class="fas fa-play mr-2"></i> Start
                        </button>
                        <button id="pauseBtn" disabled class="px-6 py-3 bg-gray-200 dark:bg-gray-700 text-gray-600 dark:text-gray-300 font-medium rounded-lg shadow-sm focus:outline-none">
                            <i class="fas fa-pause mr-2"></i> Pause
                        </button>
                        <button id="resetBtn" class="px-6 py-3 bg-white dark:bg-gray-700 border border-gray-300 dark:border-gray-600 text-gray-700 dark:text-white font-medium rounded-lg shadow-sm hover:bg-gray-50 dark:hover:bg-gray-600 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500">
                            <i class="fas fa-redo mr-2"></i> Reset
                        </button>
                    </div>
                    
                    <!-- Task Input -->
                    <div class="mt-8">
                        <label for="task-input" class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-2">Current Task</label>
                        <div class="flex">
                            <input type="text" id="task-input" class="flex-1 min-w-0 block w-full px-3 py-2 rounded-l-md border border-gray-300 dark:border-gray-600 focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm dark:bg-gray-700 dark:text-white" placeholder="What are you working on?">
                            <button id="add-task-btn" class="inline-flex items-center px-4 py-2 border border-transparent text-sm font-medium rounded-r-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500">
                                Add
                            </button>
                        </div>
                    </div>
                    
                    <!-- Current Task Display -->
                    <div id="current-task-container" class="mt-4">
                        <div class="flex items-center justify-between bg-indigo-50 dark:bg-indigo-900/30 p-3 rounded-lg">
                            <div class="flex items-center">
                                <i class="fas fa-check-circle text-indigo-600 dark:text-indigo-400 mr-3"></i>
                                <span id="current-task-text" class="font-medium dark:text-white">Working on project presentation</span>
                            </div>
                            <button id="clear-task-btn" class="text-gray-500 hover:text-gray-700 dark:text-gray-300 dark:hover:text-gray-100">
                                <i class="fas fa-times"></i>
                            </button>
                        </div>
                    </div>
                </div>
                
                <!-- Stats and Sessions Section -->
                <div class="w-full md:w-1/3">
                    <div class="bg-white dark:bg-gray-800 rounded-xl shadow-sm p-6 mb-6">
                        <div class="flex justify-between items-center mb-4">
                            <h3 class="text-lg font-medium text-gray-900 dark:text-white">Today's Progress</h3>
                        </div>
                        <div class="grid grid-cols-3 gap-4 text-center">
                            <div class="bg-indigo-50 dark:bg-indigo-900/30 p-3 rounded-lg relative">
                                <button id="edit-pomodoros" class="absolute top-2 right-2 text-gray-500 hover:text-indigo-600 dark:text-gray-300 dark:hover:text-indigo-400">
                                    <i class="fas fa-edit text-xs"></i>
                                </button>
                                <p class="text-sm text-indigo-600 dark:text-indigo-400 font-medium">Pomodoros</p>
                                <p id="today-pomodoros" class="text-2xl font-bold text-gray-800 dark:text-white">2</p>
                            </div>
                            <div class="bg-blue-50 dark:bg-blue-900/30 p-3 rounded-lg">
                                <p class="text-sm text-blue-600 dark:text-blue-400 font-medium">Focus Time</p>
                                <p id="today-focus-time" class="text-2xl font-bold text-gray-800 dark:text-white">75m</p>
                            </div>
                            <div class="bg-green-50 dark:bg-green-900/30 p-3 rounded-lg">
                                <p class="text-sm text-green-600 dark:text-green-400 font-medium">Tasks</p>
                                <p id="today-tasks" class="text-2xl font-bold text-gray-800 dark:text-white">1</p>
                            </div>
                        </div>
                    </div>
                    
                    <div class="bg-white dark:bg-gray-800 rounded-xl shadow-sm p-6">
                        <div class="flex justify-between items-center mb-4">
                            <h3 class="text-lg font-medium text-gray-900 dark:text-white">Recent Sessions</h3>
                            <button id="view-all-sessions" class="text-sm text-indigo-600 hover:text-indigo-800 dark:text-indigo-400 dark:hover:text-indigo-300">View All</button>
                        </div>
                        <div id="sessions-list" class="space-y-3">
                            <div class="session-item bg-gray-50 dark:bg-gray-700 p-3 rounded-lg">
                                <div class="flex items-center">
                                    <div class="flex-shrink-0">
                                        <div class="h-8 w-8 rounded-full bg-indigo-100 dark:bg-indigo-900 flex items-center justify-center">
                                            <i class="fas fa-check text-indigo-600 dark:text-indigo-400"></i>
                                        </div>
                                    </div>
                                    <div class="ml-3">
                                        <p class="text-sm font-medium text-gray-900 dark:text-white">Working on project presentation</p>
                                        <p class="text-xs text-gray-500 dark:text-gray-400">8/30/2025 • 50 min</p>
                                    </div>
                                </div>
                            </div>
                            <div class="session-item bg-gray-50 dark:bg-gray-700 p-3 rounded-lg">
                                <div class="flex items-center">
                                    <div class="flex-shrink-0">
                                        <div class="h-8 w-8 rounded-full bg-indigo-100 dark:bg-indigo-900 flex items-center justify-center">
                                            <i class="fas fa-check text-indigo-600 dark:text-indigo-400"></i>
                                        </div>
                                    </div>
                                    <div class="ml-3">
                                        <p class="text-sm font-medium text-gray-900 dark:text-white">Working on project presentation</p>
                                        <p class="text-xs text-gray-500 dark:text-gray-400">8/30/2025 • 25 min</p>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        
        <!-- Progress Tab -->
        <div id="progress-tab" class="tab-content">
            <div class="bg-white dark:bg-gray-800 rounded-xl shadow-sm p-6 mb-6">
                <div class="flex justify-between items-center mb-6">
                    <h2 class="text-2xl font-bold text-gray-900 dark:text-white">Progress Dashboard</h2>
                    <div class="flex items-center">
                        <span class="mr-2 text-gray-600 dark:text-gray-300">Filter:</span>
                        <select class="bg-white dark:bg-gray-700 border border-gray-300 dark:border-gray-600 rounded-md px-3 py-1">
                            <option>Today</option>
                            <option>This Week</option>
                            <option>This Month</option>
                            <option>All Time</option>
                        </select>
                    </div>
                </div>
                
                <div class="grid grid-cols-1 md:grid-cols-3 gap-6 mb-8">
                    <div class="bg-indigo-50 dark:bg-indigo-900/20 p-6 rounded-xl">
                        <div class="flex justify-between items-start">
                            <div>
                                <h3 class="text-lg font-medium text-indigo-600 dark:text-indigo-400">Pomodoros Completed</h3>
                                <p class="text-4xl font-bold text-gray-800 dark:text-white mt-2">24</p>
                            </div>
                            <div class="bg-indigo-100 dark:bg-indigo-800/30 p-3 rounded-full">
                                <i class="fas fa-check-circle text-indigo-600 dark:text-indigo-400 text-xl"></i>
                            </div>
                        </div>
                        <div class="mt-4">
                            <div class="flex justify-between text-sm text-gray-600 dark:text-gray-300 mb-1">
                                <span>This Week</span>
                                <span>+18%</span>
                            </div>
                            <div class="w-full bg-gray-200 dark:bg-gray-700 rounded-full h-2">
                                <div class="bg-indigo-600 h-2 rounded-full" style="width: 75%"></div>
                            </div>
                        </div>
                    </div>
                    
                    <div class="bg-blue-50 dark:bg-blue-900/20 p-6 rounded-xl">
                        <div class="flex justify-between items-start">
                            <div>
                                <h3 class="text-lg font-medium text-blue-600 dark:text-blue-400">Total Focus Time</h3>
                                <p class="text-4xl font-bold text-gray-800 dark:text-white mt-2">18h 45m</p>
                            </div>
                            <div class="bg-blue-100 dark:bg-blue-800/30 p-3 rounded-full">
                                <i class="fas fa-clock text-blue-600 dark:text-blue-400 text-xl"></i>
                            </div>
                        </div>
                        <div class="mt-4">
                            <div class="flex justify-between text-sm text-gray-600 dark:text-gray-300 mb-1">
                                <span>This Week</span>
                                <span>+22%</span>
                            </div>
                            <div class="w-full bg-gray-200 dark:bg-gray-700 rounded-full h-2">
                                <div class="bg-blue-600 h-2 rounded-full" style="width: 65%"></div>
                            </div>
                        </div>
                    </div>
                    
                    <div class="bg-green-50 dark:bg-green-900/20 p-6 rounded-xl">
                        <div class="flex justify-between items-start">
                            <div>
                                <h3 class="text-lg font-medium text-green-600 dark:text-green-400">Tasks Completed</h3>
                                <p class="text-4xl font-bold text-gray-800 dark:text-white mt-2">15</p>
                            </div>
                            <div class="bg-green-100 dark:bg-green-800/30 p-3 rounded-full">
                                <i class="fas fa-tasks text-green-600 dark:text-green-400 text-xl"></i>
                            </div>
                        </div>
                        <div class="mt-4">
                            <div class="flex justify-between text-sm text-gray-600 dark:text-gray-300 mb-1">
                                <span>This Week</span>
                                <span>+12%</span>
                            </div>
                            <div class="w-full bg-gray-200 dark:bg-gray-700 rounded-full h-2">
                                <div class="bg-green-600 h-2 rounded-full" style="width: 50%"></div>
                            </div>
                        </div>
                    </div>
                </div>
                
                <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
                    <div class="bg-white dark:bg-gray-700 p-6 rounded-xl shadow">
                        <h3 class="text-lg font-medium text-gray-900 dark:text-white mb-4">Daily Pomodoro Trend</h3>
                        <div class="h-64 flex items-end space-x-2">
                            <div class="flex-1 flex flex-col items-center">
                                <div class="bg-indigo-600 w-full rounded-t-md" style="height: 80%"></div>
                                <span class="mt-2 text-sm text-gray-600 dark:text-gray-300">Mon</span>
                            </div>
                            <div class="flex-1 flex flex-col items-center">
                                <div class="bg-indigo-600 w-full rounded-t-md" style="height: 65%"></div>
                                <span class="mt-2 text-sm text-gray-600 dark:text-gray-300">Tue</span>
                            </div>
                            <div class="flex-1 flex flex-col items-center">
                                <div class="bg-indigo-600 w-full rounded-t-md" style="height: 90%"></div>
                                <span class="mt-2 text-sm text-gray-600 dark:text-gray-300">Wed</span>
                            </div>
                            <div class="flex-1 flex flex-col items-center">
                                <div class="bg-indigo-600 w-full rounded-t-md" style="height: 75%"></div>
                                <span class="mt-2 text-sm text-gray-600 dark:text-gray-300">Thu</span>
                            </div>
                            <div class="flex-1 flex flex-col items-center">
                                <div class="bg-indigo-600 w-full rounded-t-md" style="height: 95%"></div>
                                <span class="mt-2 text-sm text-gray-600 dark:text-gray-300">Fri</span>
                            </div>
                            <div class="flex-1 flex flex-col items-center">
                                <div class="bg-indigo-600 w-full rounded-t-md" style="height: 50%"></div>
                                <span class="mt-2 text-sm text-gray-600 dark:text-gray-300">Sat</span>
                            </div>
                            <div class="flex-1 flex flex-col items-center">
                                <div class="bg-indigo-600 w-full rounded-t-md" style="height: 30%"></div>
                                <span class="mt-2 text-sm text-gray-600 dark:text-gray-300">Sun</span>
                            </div>
                        </div>
                    </div>
                    
                    <div class="bg-white dark:bg-gray-700 p-6 rounded-xl shadow">
                        <div class="flex justify-between items-center mb-4">
                            <h3 class="text-lg font-medium text-gray-900 dark:text-white">Recent Activity</h3>
                            <button class="text-sm text-indigo-600 hover:text-indigo-800 dark:text-indigo-400 dark:hover:text-indigo-300">View All</button>
                        </div>
                        <div class="space-y-4">
                            <div class="flex items-start">
                                <div class="bg-indigo-100 dark:bg-indigo-800/30 p-2 rounded-full mr-3">
                                    <i class="fas fa-check-circle text-indigo-600 dark:text-indigo-400"></i>
                                </div>
                                <div>
                                    <p class="font-medium text-gray-900 dark:text-white">Completed Focus Session</p>
                                    <p class="text-sm text-gray-600 dark:text-gray-300">Project Presentation - 50 min</p>
                                    <p class="text-xs text-gray-500 dark:text-gray-400 mt-1">Today at 10:30 AM</p>
                                </div>
                            </div>
                            <div class="flex items-start">
                                <div class="bg-green-100 dark:bg-green-800/30 p-2 rounded-full mr-3">
                                    <i class="fas fa-tasks text-green-600 dark:text-green-400"></i>
                                </div>
                                <div>
                                    <p class="font-medium text-gray-900 dark:text-white">Task Completed</p>
                                    <p class="text-sm text-gray-600 dark:text-gray-300">Create presentation slides</p>
                                    <p class="text-xs text-gray-500 dark:text-gray-400 mt-1">Today at 9:45 AM</p>
                                </div>
                            </div>
                            <div class="flex items-start">
                                <div class="bg-blue-100 dark:bg-blue-800/30 p-2 rounded-full mr-3">
                                    <i class="fas fa-clock text-blue-600 dark:text-blue-400"></i>
                                </div>
                                <div>
                                    <p class="font-medium text-gray-900 dark:text-white">Started Focus Session</p>
                                    <p class="text-sm text-gray-600 dark:text-gray-300">Project Presentation</p>
                                    <p class="text-xs text-gray-500 dark:text-gray-400 mt-1">Today at 9:30 AM</p>
                                </div>
                            </div>
                            <div class="flex items-start">
                                <div class="bg-indigo-100 dark:bg-indigo-800/30 p-2 rounded-full mr-3">
                                    <i class="fas fa-plus text-indigo-600 dark:text-indigo-400"></i>
                                </div>
                                <div>
                                    <p class="font-medium text-gray-900 dark:text-white">Task Added</p>
                                    <p class="text-sm text-gray-600 dark:text-gray-300">Create presentation slides</p>
                                    <p class="text-xs text-gray-500 dark:text-gray-400 mt-1">Today at 9:15 AM</p>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </main>
    
    <!-- Settings Modal -->
    <div id="settingsModal" class="modal fixed inset-0 z-10 overflow-y-auto hidden">
        <div class="flex items-center justify-center min-h-screen pt-4 px-4 pb-20 text-center sm:block sm:p-0">
            <div class="fixed inset-0 transition-opacity" aria-hidden="true">
                <div class="absolute inset-0 bg-gray-500 opacity-75"></div>
            </div>
            <span class="hidden sm:inline-block sm:align-middle sm:h-screen" aria-hidden="true">&#8203;</span>
            <div class="inline-block align-bottom bg-white dark:bg-gray-800 rounded-lg text-left overflow-hidden shadow-xl transform transition-all sm:my-8 sm:align-middle sm:max-w-lg sm:w-full">
                <div class="bg-white dark:bg-gray-800 px-4 pt-5 pb-4 sm:p-6 sm:pb-4">
                    <div class="flex justify-between items-center mb-4">
                        <h3 class="text-lg leading-6 font-medium text-gray-900 dark:text-white">Timer Settings</h3>
                        <button id="closeSettings" class="text-gray-400 hover:text-gray-500 dark:text-gray-300 dark:hover:text-gray-200">
                            <i class="fas fa-times"></i>
                        </button>
                    </div>
                    <div class="space-y-4">
                        <div>
                            <label for="pomodoro-time" class="block text-sm font-medium text-gray-700 dark:text-gray-300">Focus Time (minutes)</label>
                            <input type="number" id="pomodoro-time" min="1" max="120" value="50" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500 sm:text-sm dark:bg-gray-700 dark:text-white dark:border-gray-600">
                        </div>
                        <div>
                            <label for="short-break-time" class="block text-sm font-medium text-gray-700 dark:text-gray-300">Short Break (minutes)</label>
                            <input type="number" id="short-break-time" min="1" max="30" value="5" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500 sm:text-sm dark:bg-gray-700 dark:text-white dark:border-gray-600">
                        </div>
                        <div>
                            <label for="long-break-time" class="block text-sm font-medium text-gray-700 dark:text-gray-300">Long Break (minutes)</label>
                            <input type="number" id="long-break-time" min="1" max="60" value="20" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500 sm:text-sm dark:bg-gray-700 dark:text-white dark:border-gray-600">
                        </div>
                        <div>
                            <label for="pomodoros-before-long-break" class="block text-sm font-medium text-gray-700 dark:text-gray-300">Pomodoros before long break</label>
                            <input type="number" id="pomodoros-before-long-break" min="1" max="10" value="4" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500 sm:text-sm dark:bg-gray-700 dark:text-white dark:border-gray-600">
                        </div>
                        <div class="flex items-start">
                            <div class="flex items-center h-5">
                                <input id="auto-start-breaks" type="checkbox" class="focus:ring-indigo-500 h-4 w-4 text-indigo-600 border-gray-300 rounded dark:bg-gray-700 dark:border-gray-600">
                            </div>
                            <div class="ml-3 text-sm">
                                <label for="auto-start-breaks" class="font-medium text-gray-700 dark:text-gray-300">Auto-start breaks</label>
                                <p class="text-gray-500 dark:text-gray-400">Timer automatically switches to break when focus time ends</p>
                            </div>
                        </div>
                        <div class="flex items-start">
                            <div class="flex items-center h-5">
                                <input id="auto-start-pomodoros" type="checkbox" class="focus:ring-indigo-500 h-4 w-4 text-indigo-600 border-gray-300 rounded dark:bg-gray-700 dark:border-gray-600">
                            </div>
                            <div class="ml-3 text-sm">
                                <label for="auto-start-pomodoros" class="font-medium text-gray-700 dark:text-gray-300">Auto-start focus</label>
                                <p class="text-gray-500 dark:text-gray-400">Timer automatically switches to focus when break ends</p>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="bg-gray-50 dark:bg-gray-700 px-4 py-3 sm:px-6 sm:flex sm:flex-row-reverse">
                    <button id="saveSettingsBtn" type="button" class="w-full inline-flex justify-center rounded-md border border-transparent shadow-sm px-4 py-2 bg-indigo-600 text-base font-medium text-white hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 sm:ml-3 sm:w-auto sm:text-sm">
                        Save Settings
                    </button>
                    <button id="cancelSettings" type="button" class="mt-3 w-full inline-flex justify-center rounded-md border border-gray-300 shadow-sm px-4 py-2 bg-white dark:bg-gray-600 dark:text-white text-base font-medium text-gray-700 hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 sm:mt-0 sm:ml-3 sm:w-auto sm:text-sm">
                        Cancel
                    </button>
                </div>
            </div>
        </div>
    </div>
    
    <!-- Pomodoro Edit Modal -->
    <div id="pomodoroEditModal" class="modal fixed inset-0 z-10 overflow-y-auto hidden">
        <div class="flex items-center justify-center min-h-screen pt-4 px-4 pb-20 text-center sm:block sm:p-0">
            <div class="fixed inset-0 transition-opacity" aria-hidden="true">
                <div class="absolute inset-0 bg-gray-500 opacity-75"></div>
            </div>
            <span class="hidden sm:inline-block sm:align-middle sm:h-screen" aria-hidden="true">&#8203;</span>
            <div class="inline-block align-bottom bg-white dark:bg-gray-800 rounded-lg text-left overflow-hidden shadow-xl transform transition-all sm:my-8 sm:align-middle sm:max-w-sm sm:w-full">
                <div class="bg-white dark:bg-gray-800 px-4 pt-5 pb-4 sm:p-6 sm:pb-4">
                    <div class="flex justify-between items-center mb-4">
                        <h3 class="text-lg leading-6 font-medium text-gray-900 dark:text-white">Edit Pomodoros</h3>
                        <button id="closePomodoroEdit" class="text-gray-400 hover:text-gray-500 dark:text-gray-300 dark:hover:text-gray-200">
                            <i class="fas fa-times"></i>
                        </button>
                    </div>
                    <div class="mb-6">
                        <p class="text-sm text-gray-600 dark:text-gray-300 mb-4">
                            Set how many Pomodoro sessions you want to complete before taking a long break.
                        </p>
                        <div class="flex items-center justify-center">
                            <button id="decrement-pomodoros" class="bg-gray-200 dark:bg-gray-700 text-gray-700 dark:text-white rounded-l-lg px-4 py-2">
                                <i class="fas fa-minus"></i>
                            </button>
                            <input type="number" id="pomodoro-count-input" min="1" max="10" value="4" class="w-16 text-center border-t border-b border-gray-300 dark:border-gray-600 py-2 bg-white dark:bg-gray-700 text-gray-900 dark:text-white">
                            <button id="increment-pomodoros" class="bg-gray-200 dark:bg-gray-700 text-gray-700 dark:text-white rounded-r-lg px-4 py-2">
                                <i class="fas fa-plus"></i>
                            </button>
                        </div>
                        <p class="text-xs text-gray-500 dark:text-gray-400 mt-2 text-center">Minimum: 1, Maximum: 10</p>
                    </div>
                </div>
                <div class="bg-gray-50 dark:bg-gray-700 px-4 py-3 sm:px-6 sm:flex sm:flex-row-reverse">
                    <button id="savePomodoroCount" type="button" class="w-full inline-flex justify-center rounded-md border border-transparent shadow-sm px-4 py-2 bg-indigo-600 text-base font-medium text-white hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 sm:ml-3 sm:w-auto sm:text-sm">
                        Save Changes
                    </button>
                    <button id="cancelPomodoroEdit" type="button" class="mt-3 w-full inline-flex justify-center rounded-md border border-gray-300 shadow-sm px-4 py-2 bg-white dark:bg-gray-600 dark:text-white text-base font-medium text-gray-700 hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 sm:mt-0 sm:ml-3 sm:w-auto sm:text-sm">
                        Cancel
                    </button>
                </div>
            </div>
        </div>
    </div>
    
    <!-- Notification -->
    <div id="notification" class="fixed bottom-4 right-4 hidden">
        <div class="bg-green-500 text-white px-4 py-3 rounded-lg shadow-lg flex items-center">
            <i class="fas fa-check-circle mr-2"></i>
            <span id="notification-message">Timer completed successfully!</span>
            <button id="close-notification" class="ml-4 text-white hover:text-gray-200">
                <i class="fas fa-times"></i>
            </button>
        </div>
    </div>
    
    <script>
        // DOM Elements
        const timerDisplay = document.getElementById('time-display');
        const timerLabel = document.getElementById('timer-label');
        const progressCircle = document.getElementById('progress-circle');
        const startBtn = document.getElementById('startBtn');
        const pauseBtn = document.getElementById('pauseBtn');
        const resetBtn = document.getElementById('resetBtn');
        const modeBtns = document.querySelectorAll('.mode-btn');
        const taskInput = document.getElementById('task-input');
        const addTaskBtn = document.getElementById('add-task-btn');
        const currentTaskContainer = document.getElementById('current-task-container');
        const currentTaskText = document.getElementById('current-task-text');
        const clearTaskBtn = document.getElementById('clear-task-btn');
        const todayPomodoros = document.getElementById('today-pomodoros');
        const todayFocusTime = document.getElementById('today-focus-time');
        const todayTasks = document.getElementById('today-tasks');
        const sessionsList = document.getElementById('sessions-list');
        const darkModeToggle = document.getElementById('darkModeToggle');
        const darkModeIcon = document.getElementById('darkModeIcon');
        const editPomodorosBtn = document.getElementById('edit-pomodoros');
        const pomodoroEditModal = document.getElementById('pomodoroEditModal');
        const closePomodoroEdit = document.getElementById('closePomodoroEdit');
        const cancelPomodoroEdit = document.getElementById('cancelPomodoroEdit');
        const pomodoroCountInput = document.getElementById('pomodoro-count-input');
        const decrementPomodoros = document.getElementById('decrement-pomodoros');
        const incrementPomodoros = document.getElementById('increment-pomodoros');
        const savePomodoroCount = document.getElementById('savePomodoroCount');
        
        // Modal elements
        const settingsModal = document.getElementById('settingsModal');
        const settingsBtn = document.getElementById('settingsBtn');
        const closeSettings = document.getElementById('closeSettings');
        const saveSettingsBtn = document.getElementById('saveSettingsBtn');
        const cancelSettings = document.getElementById('cancelSettings');
        
        // Tab navigation elements
        const navLinks = document.querySelectorAll('.nav-link');
        const timerTab = document.getElementById('timer-tab');
        const progressTab = document.getElementById('progress-tab');
        
        // Timer variables
        let timer;
        let timeLeft = 0;
        let totalTime = 50 * 60; // 50 minutes in seconds
        let isRunning = false;
        let currentMode = 'pomodoro';
        let pomodoroCount = 2;
        
        // Settings
        let settings = {
            pomodoroTime: 50,
            shortBreakTime: 5,
            longBreakTime: 20,
            pomodorosBeforeLongBreak: 4,
            autoStartBreaks: true,
            autoStartPomodoros: false,
            darkMode: false
        };
        
        // Initialize the app
        function init() {
            loadSettings();
            setupEventListeners();
            updateTimerDisplay();
            updateProgressCircle();
            applyDarkMode();
            
            // Initialize tabs
            switchTab('timer');
        }
        
        // Load settings from localStorage
        function loadSettings() {
            const savedSettings = localStorage.getItem('pomodoroSettings');
            if (savedSettings) {
                settings = JSON.parse(savedSettings);
                document.getElementById('pomodoro-time').value = settings.pomodoroTime;
                document.getElementById('short-break-time').value = settings.shortBreakTime;
                document.getElementById('long-break-time').value = settings.longBreakTime;
                document.getElementById('pomodoros-before-long-break').value = settings.pomodorosBeforeLongBreak;
                document.getElementById('auto-start-breaks').checked = settings.autoStartBreaks;
                document.getElementById('auto-start-pomodoros').checked = settings.autoStartPomodoros;
                
                // Set dark mode if enabled
                if (settings.darkMode) {
                    document.documentElement.classList.add('dark');
                    darkModeIcon.classList.remove('fa-moon');
                    darkModeIcon.classList.add('fa-sun');
                }
            }
        }
        
        // Save settings to localStorage
        function saveSettingsToStorage() {
            localStorage.setItem('pomodoroSettings', JSON.stringify(settings));
        }
        
        // Set up event listeners
        function setupEventListeners() {
            // Timer controls
            startBtn.addEventListener('click', startTimer);
            pauseBtn.addEventListener('click', pauseTimer);
            resetBtn.addEventListener('click', resetTimer);
            
            // Mode buttons
            modeBtns.forEach(btn => {
                btn.addEventListener('click', () => switchMode(btn.dataset.mode));
            });
            
            // Task management
            addTaskBtn.addEventListener('click', addTask);
            clearTaskBtn.addEventListener('click', clearTask);
            taskInput.addEventListener('keypress', (e) => {
                if (e.key === 'Enter') addTask();
            });
            
            // Settings modal
            settingsBtn.addEventListener('click', openSettingsModal);
            closeSettings.addEventListener('click', closeSettingsModal);
            saveSettingsBtn.addEventListener('click', saveSettingsHandler);
            cancelSettings.addEventListener('click', closeSettingsModal);
            
            // Dark mode toggle
            darkModeToggle.addEventListener('click', toggleDarkMode);
            
            // Pomodoro count edit
            editPomodorosBtn.addEventListener('click', openPomodoroEditModal);
            closePomodoroEdit.addEventListener('click', closePomodoroEditModal);
            cancelPomodoroEdit.addEventListener('click', closePomodoroEditModal);
            decrementPomodoros.addEventListener('click', () => {
                let value = parseInt(pomodoroCountInput.value);
                if (value > 1) {
                    pomodoroCountInput.value = value - 1;
                }
            });
            incrementPomodoros.addEventListener('click', () => {
                let value = parseInt(pomodoroCountInput.value);
                if (value < 10) {
                    pomodoroCountInput.value = value + 1;
                }
            });
            savePomodoroCount.addEventListener('click', savePomodoroCountHandler);
            
            // Tab navigation
            navLinks.forEach(link => {
                link.addEventListener('click', (e) => {
                    e.preventDefault();
                    const tab = link.dataset.tab;
                    switchTab(tab);
                });
            });
        }
        
        // Tab switching function
        function switchTab(tabName) {
            // Update UI
            navLinks.forEach(link => {
                if (link.dataset.tab === tabName) {
                    link.classList.add('active');
                    link.classList.remove('text-gray-500');
                    link.classList.add('text-gray-900', 'dark:text-white');
                    link.classList.remove('border-transparent');
                    link.classList.add('border-indigo-500');
                } else {
                    link.classList.remove('active');
                    link.classList.add('text-gray-500', 'dark:text-gray-300');
                    link.classList.remove('text-gray-900', 'dark:text-white');
                    link.classList.add('border-transparent');
                    link.classList.remove('border-indigo-500');
                }
            });
            
            // Show active tab content
            if (tabName === 'timer') {
                timerTab.classList.add('active');
                progressTab.classList.remove('active');
            } else if (tabName === 'progress') {
                progressTab.classList.add('active');
                timerTab.classList.remove('active');
            }
        }
        
        // Timer functions
        function startTimer() {
            if (!isRunning) {
                isRunning = true;
                startBtn.disabled = true;
                pauseBtn.disabled = false;
                
                timer = setInterval(() => {
                    timeLeft--;
                    updateTimerDisplay();
                    updateProgressCircle();
                    
                    if (timeLeft <= 0) {
                        clearInterval(timer);
                        timerComplete();
                    }
                }, 1000);
            }
        }
        
        function pauseTimer() {
            if (isRunning) {
                clearInterval(timer);
                isRunning = false;
                startBtn.disabled = false;
                pauseBtn.disabled = true;
            }
        }
        
        function resetTimer() {
            clearInterval(timer);
            isRunning = false;
            startBtn.disabled = false;
            pauseBtn.disabled = true;
            
            switch (currentMode) {
                case 'pomodoro':
                    timeLeft = settings.pomodoroTime * 60;
                    break;
                case 'shortBreak':
                    timeLeft = settings.shortBreakTime * 60;
                    break;
                case 'longBreak':
                    timeLeft = settings.longBreakTime * 60;
                    break;
            }
            
            updateTimerDisplay();
            updateProgressCircle();
        }
        
        function timerComplete() {
            isRunning = false;
            startBtn.disabled = false;
            pauseBtn.disabled = true;
            
            // Add pulse animation
            timerDisplay.classList.add('pulse');
            
            // Show notification
            showNotification(`${currentMode === 'pomodoro' ? 'Focus session' : 'Break'} completed!`);
            
            // Log the session if it was a pomodoro
            if (currentMode === 'pomodoro') {
                pomodoroCount++;
                todayPomodoros.textContent = pomodoroCount;
                
                // Update focus time
                todayFocusTime.textContent = `${pomodoroCount * 25}m`;
            }
            
            // Remove pulse animation after 5 seconds
            setTimeout(() => {
                timerDisplay.classList.remove('pulse');
            }, 5000);
        }
        
        function switchMode(mode) {
            // Reset the current timer if running
            if (isRunning) {
                clearInterval(timer);
                isRunning = false;
            }
            
            // Update mode and UI
            currentMode = mode;
            updateActiveModeButton();
            
            // Set the appropriate time
            switch (mode) {
                case 'pomodoro':
                    timeLeft = settings.pomodoroTime * 60;
                    timerLabel.textContent = 'Focus Time';
                    break;
                case 'shortBreak':
                    timeLeft = settings.shortBreakTime * 60;
                    timerLabel.textContent = 'Short Break';
                    break;
                case 'longBreak':
                    timeLeft = settings.longBreakTime * 60;
                    timerLabel.textContent = 'Long Break';
                    break;
            }
            
            totalTime = timeLeft;
            updateTimerDisplay();
            updateProgressCircle();
            
            // Reset button states
            startBtn.disabled = false;
            pauseBtn.disabled = true;
        }
        
        function updateActiveModeButton() {
            modeBtns.forEach(btn => {
                if (btn.dataset.mode === currentMode) {
                    btn.classList.remove('bg-white', 'text-gray-700', 'hover:bg-gray-50', 'dark:bg-gray-700', 'dark:text-white', 'dark:hover:bg-gray-600');
                    btn.classList.add('bg-indigo-600', 'text-white');
                } else {
                    btn.classList.remove('bg-indigo-600', 'text-white');
                    btn.classList.add('bg-white', 'text-gray-700', 'hover:bg-gray-50', 'dark:bg-gray-700', 'dark:text-white', 'dark:hover:bg-gray-600');
                }
            });
        }
        
        function updateTimerDisplay() {
            const minutes = Math.floor(timeLeft / 60);
            const seconds = timeLeft % 60;
            timerDisplay.textContent = `${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}`;
        }
        
        function updateProgressCircle() {
            const circumference = 2 * Math.PI * 40;
            const offset = circumference - (timeLeft / totalTime) * circumference;
            progressCircle.style.strokeDasharray = circumference;
            progressCircle.style.strokeDashoffset = offset;
        }
        
        // Task management
        function addTask() {
            const task = taskInput.value.trim();
            if (task) {
                currentTaskText.textContent = task;
                taskInput.value = '';
                todayTasks.textContent = '1';
            }
        }
        
        function clearTask() {
            currentTaskText.textContent = 'No task selected';
            todayTasks.textContent = '0';
        }
        
        // Modal functions
        function openSettingsModal() {
            settingsModal.classList.remove('hidden');
            document.body.classList.add('overflow-hidden');
        }
        
        function closeSettingsModal() {
            settingsModal.classList.add('hidden');
            document.body.classList.remove('overflow-hidden');
        }
        
        function openPomodoroEditModal() {
            pomodoroCountInput.value = settings.pomodorosBeforeLongBreak;
            pomodoroEditModal.classList.remove('hidden');
            document.body.classList.add('overflow-hidden');
        }
        
        function closePomodoroEditModal() {
            pomodoroEditModal.classList.add('hidden');
            document.body.classList.remove('overflow-hidden');
        }
        
        // Settings functions
        function saveSettingsHandler() {
            settings.pomodoroTime = parseInt(document.getElementById('pomodoro-time').value);
            settings.shortBreakTime = parseInt(document.getElementById('short-break-time').value);
            settings.longBreakTime = parseInt(document.getElementById('long-break-time').value);
            settings.pomodorosBeforeLongBreak = parseInt(document.getElementById('pomodoros-before-long-break').value);
            settings.autoStartBreaks = document.getElementById('auto-start-breaks').checked;
            settings.autoStartPomodoros = document.getElementById('auto-start-pomodoros').checked;
            
            saveSettingsToStorage();
            closeSettingsModal();
            resetTimer(); // Apply new settings immediately
            
            showNotification('Settings saved successfully!');
        }
        
        function savePomodoroCountHandler() {
            const newCount = parseInt(pomodoroCountInput.value);
            if (newCount >= 1 && newCount <= 10) {
                settings.pomodorosBeforeLongBreak = newCount;
                document.getElementById('pomodoros-before-long-break').value = newCount;
                saveSettingsToStorage();
                closePomodoroEditModal();
                showNotification('Pomodoro count updated!');
            } else {
                showNotification('Please enter a value between 1 and 10', 'error');
            }
        }
        
        // Dark mode functions
        function toggleDarkMode() {
            settings.darkMode = !settings.darkMode;
            saveSettingsToStorage();
            applyDarkMode();
        }
        
        function applyDarkMode() {
            if (settings.darkMode) {
                document.documentElement.classList.add('dark');
                darkModeIcon.classList.remove('fa-moon');
                darkModeIcon.classList.add('fa-sun');
            } else {
                document.documentElement.classList.remove('dark');
                darkModeIcon.classList.remove('fa-sun');
                darkModeIcon.classList.add('fa-moon');
            }
        }
        
        // Notification functions
        function showNotification(message, type = 'success') {
            const notification = document.getElementById('notification');
            const notificationMessage = document.getElementById('notification-message');
            
            notificationMessage.textContent = message;
            
            // Set color based on type
            if (type === 'error') {
                notification.classList.remove('bg-green-500');
                notification.classList.add('bg-red-500');
            } else {
                notification.classList.remove('bg-red-500');
                notification.classList.add('bg-green-500');
            }
            
            notification.classList.remove('hidden');
            
            // Auto-hide after 5 seconds
            setTimeout(() => {
                notification.classList.add('hidden');
            }, 5000);
        }
        
        // Initialize the app
        document.addEventListener('DOMContentLoaded', init);
    </script>
</body>
</html>
