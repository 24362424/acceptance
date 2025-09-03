<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Ethio-Djibouti Railway Rolling Stock Management System</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/js/all.min.js"></script>
    <style>
        .team-card { transition: all 0.3s ease; }
        .team-card:hover { transform: translateY(-4px); box-shadow: 0 20px 25px -5px rgba(0, 0, 0, 0.1); }
        .chart-container { position: relative; height: 300px; }
        .nav-item.active { background-color: #5D5CDE; color: white; border-radius: 0.5rem; }
        .form-section { background: rgba(255,255,255,0.05); backdrop-filter: blur(10px); }
        .gradient-bg { background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); }
        .data-entry-success { animation: slideInUp 0.5s ease-out; }
        @keyframes slideInUp { from { transform: translateY(30px); opacity: 0; } to { transform: translateY(0); opacity: 1; } }
        .loading-spinner { animation: spin 1s linear infinite; }
        @keyframes spin { from { transform: rotate(0deg); } to { transform: rotate(360deg); } }
    </style>
    <script>
        tailwind.config = {
            darkMode: 'class',
            theme: {
                extend: {
                    colors: {
                        primary: '#5D5CDE',
                        secondary: '#8B5CF6',
                        accent: '#06B6D4',
                        success: '#10B981',
                        warning: '#F59E0B',
                        danger: '#EF4444',
                        light: '#FFFFFF',
                        dark: '#181818',
                    },
                    fontFamily: {
                        'railway': ['Inter', 'system-ui', 'sans-serif'],
                    }
                }
            }
        }
    </script>
</head>
<body class="bg-gray-50 dark:bg-gray-900 transition-colors duration-300 font-railway">
    <!-- Loading Screen -->
    <div id="loadingScreen" class="fixed inset-0 bg-primary flex items-center justify-center z-50">
        <div class="text-center text-white">
            <i class="fas fa-train text-6xl mb-4 loading-spinner"></i>
            <h2 class="text-2xl font-bold">Loading Railway Management System</h2>
            <p class="text-blue-200 mt-2">Initializing data and authentication...</p>
        </div>
    </div>

    <!-- Navigation -->
    <nav class="bg-white dark:bg-gray-800 shadow-xl sticky top-0 z-40 border-b-4 border-primary">
        <div class="max-w-7xl mx-auto px-4">
            <div class="flex justify-between items-center h-20">
                <div class="flex items-center space-x-4">
                    <div class="flex items-center space-x-3">
                        <div class="bg-primary p-3 rounded-xl">
                            <i class="fas fa-train text-white text-2xl"></i>
                        </div>
                        <div>
                            <h1 class="text-xl font-bold text-gray-800 dark:text-white">Ethio-Djibouti Railway</h1>
                            <p class="text-sm text-gray-600 dark:text-gray-400">Rolling Stock Management</p>
                        </div>
                    </div>
                </div>
                <div class="flex items-center space-x-4">
                    <div id="userInfo" class="hidden text-right mr-4">
                        <p class="text-sm font-medium text-gray-800 dark:text-white" id="userName"></p>
                        <p class="text-xs text-gray-600 dark:text-gray-400" id="userRole"></p>
                    </div>
                    <select id="teamSelector" class="px-4 py-3 border-2 border-gray-300 dark:border-gray-600 rounded-xl bg-white dark:bg-gray-700 text-gray-800 dark:text-white font-medium focus:ring-2 focus:ring-primary transition-all">
                        <option value="">🏠 Dashboard</option>
                        <option value="wagon">🚂 Wagon Team</option>
                        <option value="locomotive">🚆 Locomotive Team</option>
                        <option value="coach">🚌 Coach Team</option>
                        <option value="equipment">⚙️ Equipment Team</option>
                        <option value="safety">🛡️ Safety & Quality Team</option>
                    </select>
                    <button id="loginBtn" class="px-6 py-3 bg-primary text-white rounded-xl hover:bg-blue-600 transition-all font-medium shadow-lg">
                        <i class="fas fa-sign-in-alt mr-2"></i>Login
                    </button>
                </div>
            </div>
        </div>
    </nav>

    <!-- Main Content -->
    <div class="max-w-7xl mx-auto px-4 py-8">
        <!-- Dashboard Overview -->
        <div id="dashboardSection" class="mb-8">
            <!-- Welcome Header -->
            <div class="gradient-bg text-white p-8 rounded-2xl shadow-2xl mb-8">
                <div class="flex items-center justify-between">
                    <div>
                        <h2 class="text-4xl font-bold mb-2">
                            <i class="fas fa-tachometer-alt mr-3"></i>System Dashboard
                        </h2>
                        <p class="text-blue-100 text-lg">Comprehensive Railway Operations Management</p>
                        <p class="text-blue-200 text-sm mt-2" id="lastUpdated">Last updated: Loading...</p>
                    </div>
                    <div class="text-6xl opacity-20">
                        <i class="fas fa-railway-track"></i>
                    </div>
                </div>
            </div>
            
            <!-- Statistics Cards -->
            <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6 mb-8">
                <div class="team-card bg-gradient-to-br from-blue-500 to-blue-600 text-white p-6 rounded-2xl shadow-xl">
                    <div class="flex items-center justify-between">
                        <div>
                            <h3 class="text-lg font-semibold mb-1">Total Wagons</h3>
                            <p class="text-4xl font-bold" id="totalWagons">0</p>
                            <p class="text-blue-200 text-sm mt-1">Active units</p>
                        </div>
                        <div class="bg-white bg-opacity-20 p-4 rounded-xl">
                            <i class="fas fa-train text-3xl"></i>
                        </div>
                    </div>
                </div>
                
                <div class="team-card bg-gradient-to-br from-green-500 to-green-600 text-white p-6 rounded-2xl shadow-xl">
                    <div class="flex items-center justify-between">
                        <div>
                            <h3 class="text-lg font-semibold mb-1">Locomotives</h3>
                            <p class="text-4xl font-bold" id="totalLocomotives">0</p>
                            <p class="text-green-200 text-sm mt-1">In service</p>
                        </div>
                        <div class="bg-white bg-opacity-20 p-4 rounded-xl">
                            <i class="fas fa-locomotive text-3xl"></i>
                        </div>
                    </div>
                </div>
                
                <div class="team-card bg-gradient-to-br from-purple-500 to-purple-600 text-white p-6 rounded-2xl shadow-xl">
                    <div class="flex items-center justify-between">
                        <div>
                            <h3 class="text-lg font-semibold mb-1">Coaches</h3>
                            <p class="text-4xl font-bold" id="totalCoaches">0</p>
                            <p class="text-purple-200 text-sm mt-1">Operational</p>
                        </div>
                        <div class="bg-white bg-opacity-20 p-4 rounded-xl">
                            <i class="fas fa-subway text-3xl"></i>
                        </div>
                    </div>
                </div>
                
                <div class="team-card bg-gradient-to-br from-red-500 to-red-600 text-white p-6 rounded-2xl shadow-xl">
                    <div class="flex items-center justify-between">
                        <div>
                            <h3 class="text-lg font-semibold mb-1">Safety Incidents</h3>
                            <p class="text-4xl font-bold" id="totalIncidents">0</p>
                            <p class="text-red-200 text-sm mt-1">This month</p>
                        </div>
                        <div class="bg-white bg-opacity-20 p-4 rounded-xl">
                            <i class="fas fa-shield-alt text-3xl"></i>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Charts Section -->
            <div class="grid grid-cols-1 lg:grid-cols-2 gap-8 mb-8">
                <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl shadow-xl border border-gray-200 dark:border-gray-700">
                    <div class="flex items-center justify-between mb-6">
                        <h3 class="text-2xl font-bold text-gray-800 dark:text-white">Maintenance Status</h3>
                        <div class="bg-green-100 dark:bg-green-900 p-2 rounded-lg">
                            <i class="fas fa-tools text-green-600 dark:text-green-400"></i>
                        </div>
                    </div>
                    <div class="chart-container">
                        <canvas id="maintenanceChart"></canvas>
                    </div>
                </div>
                
                <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl shadow-xl border border-gray-200 dark:border-gray-700">
                    <div class="flex items-center justify-between mb-6">
                        <h3 class="text-2xl font-bold text-gray-800 dark:text-white">Monthly Incidents</h3>
                        <div class="bg-red-100 dark:bg-red-900 p-2 rounded-lg">
                            <i class="fas fa-exclamation-triangle text-red-600 dark:text-red-400"></i>
                        </div>
                    </div>
                    <div class="chart-container">
                        <canvas id="incidentChart"></canvas>
                    </div>
                </div>
            </div>

            <!-- Quick Actions -->
            <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl shadow-xl border border-gray-200 dark:border-gray-700">
                <h3 class="text-2xl font-bold text-gray-800 dark:text-white mb-6">
                    <i class="fas fa-bolt mr-3 text-yellow-500"></i>Quick Actions
                </h3>
                <div class="grid grid-cols-1 md:grid-cols-3 lg:grid-cols-5 gap-4">
                    <button onclick="quickAction('wagon')" class="p-4 bg-blue-50 dark:bg-blue-900 hover:bg-blue-100 dark:hover:bg-blue-800 rounded-xl transition-all text-center">
                        <i class="fas fa-train text-2xl text-blue-600 dark:text-blue-400 mb-2"></i>
                        <p class="text-sm font-medium text-gray-800 dark:text-white">Add Wagon Entry</p>
                    </button>
                    <button onclick="quickAction('locomotive')" class="p-4 bg-green-50 dark:bg-green-900 hover:bg-green-100 dark:hover:bg-green-800 rounded-xl transition-all text-center">
                        <i class="fas fa-locomotive text-2xl text-green-600 dark:text-green-400 mb-2"></i>
                        <p class="text-sm font-medium text-gray-800 dark:text-white">Add Locomotive Entry</p>
                    </button>
                    <button onclick="quickAction('coach')" class="p-4 bg-purple-50 dark:bg-purple-900 hover:bg-purple-100 dark:hover:bg-purple-800 rounded-xl transition-all text-center">
                        <i class="fas fa-subway text-2xl text-purple-600 dark:text-purple-400 mb-2"></i>
                        <p class="text-sm font-medium text-gray-800 dark:text-white">Add Coach Entry</p>
                    </button>
                    <button onclick="quickAction('safety')" class="p-4 bg-red-50 dark:bg-red-900 hover:bg-red-100 dark:hover:bg-red-800 rounded-xl transition-all text-center">
                        <i class="fas fa-shield-alt text-2xl text-red-600 dark:text-red-400 mb-2"></i>
                        <p class="text-sm font-medium text-gray-800 dark:text-white">Report Incident</p>
                    </button>
                    <button onclick="exportAllData()" class="p-4 bg-yellow-50 dark:bg-yellow-900 hover:bg-yellow-100 dark:hover:bg-yellow-800 rounded-xl transition-all text-center">
                        <i class="fas fa-download text-2xl text-yellow-600 dark:text-yellow-400 mb-2"></i>
                        <p class="text-sm font-medium text-gray-800 dark:text-white">Export All Data</p>
                    </button>
                </div>
            </div>
        </div>

        <!-- Team Sections -->
        <div id="teamSection" class="hidden">
            <!-- Team Header -->
            <div class="bg-gradient-to-r from-primary to-secondary text-white p-8 rounded-2xl shadow-2xl mb-8">
                <h2 class="text-3xl font-bold mb-2">
                    <span id="teamIcon">🚂</span>
                    <span id="teamTitle">Team Dashboard</span>
                </h2>
                <p class="text-blue-100 text-lg" id="teamDescription">Manage maintenance and operations</p>
            </div>

            <!-- Navigation Tabs -->
            <div class="bg-white dark:bg-gray-800 rounded-2xl shadow-xl mb-8 border border-gray-200 dark:border-gray-700">
                <div class="flex flex-wrap border-b border-gray-200 dark:border-gray-700 p-2">
                    <button class="nav-item px-6 py-4 font-medium text-gray-600 dark:text-gray-300 hover:text-primary transition-all rounded-lg mx-1" 
                            data-tab="dataEntry">
                        <i class="fas fa-plus-circle mr-2"></i>Data Entry
                    </button>
                    <button class="nav-item px-6 py-4 font-medium text-gray-600 dark:text-gray-300 hover:text-primary transition-all rounded-lg mx-1" 
                            data-tab="additionalTasks">
                        <i class="fas fa-tasks mr-2"></i>Additional Tasks
                    </button>
                    <button class="nav-item px-6 py-4 font-medium text-gray-600 dark:text-gray-300 hover:text-primary transition-all rounded-lg mx-1" 
                            data-tab="reports">
                        <i class="fas fa-chart-bar mr-2"></i>Reports & Analytics
                    </button>
                </div>
            </div>

            <!-- Data Entry Forms -->
            <div id="dataEntry" class="tab-content">
                <!-- Standard Rolling Stock Form -->
                <div id="standardForm" class="bg-white dark:bg-gray-800 p-8 rounded-2xl shadow-xl mb-8 border border-gray-200 dark:border-gray-700">
                    <div class="flex items-center justify-between mb-8">
                        <div>
                            <h3 class="text-3xl font-bold text-gray-800 dark:text-white">
                                <span id="formTitle">Rolling Stock</span> Maintenance Entry
                            </h3>
                            <p class="text-gray-600 dark:text-gray-400 mt-2">Record maintenance activities and track progress</p>
                        </div>
                        <div class="bg-primary bg-opacity-10 p-4 rounded-xl">
                            <i class="fas fa-clipboard-list text-3xl text-primary"></i>
                        </div>
                    </div>
                    
                    <form id="maintenanceForm" class="grid grid-cols-1 md:grid-cols-2 gap-6">
                        <div class="space-y-2">
                            <label class="block text-sm font-semibold text-gray-700 dark:text-gray-300">Unit Number *</label>
                            <input type="text" name="number" required 
                                   class="w-full text-base px-4 py-4 border-2 border-gray-300 dark:border-gray-600 rounded-xl bg-white dark:bg-gray-700 text-gray-800 dark:text-white focus:ring-2 focus:ring-primary focus:border-primary transition-all"
                                   placeholder="Enter unit number (e.g., WAG001)">
                        </div>
                        
                        <div class="space-y-2">
                            <label class="block text-sm font-semibold text-gray-700 dark:text-gray-300">Maintenance Type *</label>
                            <select name="maintenanceType" required 
                                    class="w-full text-base px-4 py-4 border-2 border-gray-300 dark:border-gray-600 rounded-xl bg-white dark:bg-gray-700 text-gray-800 dark:text-white focus:ring-2 focus:ring-primary focus:border-primary transition-all">
                                <option value="">Select maintenance type</option>
                                <option value="preventive">🔧 Preventive</option>
                                <option value="corrective">🛠️ Corrective</option>
                                <option value="emergency">🚨 Emergency</option>
                                <option value="routine">📋 Routine</option>
                            </select>
                        </div>
                        
                        <div class="md:col-span-2 space-y-2">
                            <label class="block text-sm font-semibold text-gray-700 dark:text-gray-300">Description *</label>
                            <textarea name="description" rows="4" required 
                                      class="w-full text-base px-4 py-4 border-2 border-gray-300 dark:border-gray-600 rounded-xl bg-white dark:bg-gray-700 text-gray-800 dark:text-white focus:ring-2 focus:ring-primary focus:border-primary transition-all"
                                      placeholder="Describe the maintenance work performed..."></textarea>
                        </div>
                        
                        <div class="md:col-span-2 space-y-2">
                            <label class="block text-sm font-semibold text-gray-700 dark:text-gray-300">Fault Identified</label>
                            <textarea name="fault" rows="3" 
                                      class="w-full text-base px-4 py-4 border-2 border-gray-300 dark:border-gray-600 rounded-xl bg-white dark:bg-gray-700 text-gray-800 dark:text-white focus:ring-2 focus:ring-primary focus:border-primary transition-all"
                                      placeholder="Describe any faults found during inspection..."></textarea>
                        </div>
                        
                        <div class="space-y-2">
                            <label class="block text-sm font-semibold text-gray-700 dark:text-gray-300">Spare Parts Used</label>
                            <input type="text" name="sparePart" 
                                   class="w-full text-base px-4 py-4 border-2 border-gray-300 dark:border-gray-600 rounded-xl bg-white dark:bg-gray-700 text-gray-800 dark:text-white focus:ring-2 focus:ring-primary focus:border-primary transition-all"
                                   placeholder="List spare parts used...">
                        </div>
                        
                        <div class="space-y-2">
                            <label class="block text-sm font-semibold text-gray-700 dark:text-gray-300">Status *</label>
                            <select name="status" required 
                                    class="w-full text-base px-4 py-4 border-2 border-gray-300 dark:border-gray-600 rounded-xl bg-white dark:bg-gray-700 text-gray-800 dark:text-white focus:ring-2 focus:ring-primary focus:border-primary transition-all">
                                <option value="">Select status</option>
                                <option value="pending">⏳ Pending</option>
                                <option value="in-progress">🔄 In Progress</option>
                                <option value="completed">✅ Completed</option>
                                <option value="cancelled">❌ Cancelled</option>
                            </select>
                        </div>
                        
                        <div class="space-y-2">
                            <label class="block text-sm font-semibold text-gray-700 dark:text-gray-300">Start Date *</label>
                            <input type="date" name="startDate" required 
                                   class="w-full text-base px-4 py-4 border-2 border-gray-300 dark:border-gray-600 rounded-xl bg-white dark:bg-gray-700 text-gray-800 dark:text-white focus:ring-2 focus:ring-primary focus:border-primary transition-all">
                        </div>
                        
                        <div class="space-y-2">
                            <label class="block text-sm font-semibold text-gray-700 dark:text-gray-300">Finish Date</label>
                            <input type="date" name="finishDate" 
                                   class="w-full text-base px-4 py-4 border-2 border-gray-300 dark:border-gray-600 rounded-xl bg-white dark:bg-gray-700 text-gray-800 dark:text-white focus:ring-2 focus:ring-primary focus:border-primary transition-all">
                        </div>
                        
                        <div class="md:col-span-2 space-y-2">
                            <label class="block text-sm font-semibold text-gray-700 dark:text-gray-300">Remarks</label>
                            <textarea name="remark" rows="3" 
                                      class="w-full text-base px-4 py-4 border-2 border-gray-300 dark:border-gray-600 rounded-xl bg-white dark:bg-gray-700 text-gray-800 dark:text-white focus:ring-2 focus:ring-primary focus:border-primary transition-all"
                                      placeholder="Additional notes or comments..."></textarea>
                        </div>
                        
                        <div class="md:col-span-2">
                            <button type="submit" class="w-full bg-gradient-to-r from-primary to-secondary text-white py-4 px-8 rounded-xl text-lg font-bold hover:from-blue-600 hover:to-purple-600 transition-all shadow-xl transform hover:scale-105">
                                <i class="fas fa-save mr-3"></i>Save Maintenance Entry
                            </button>
                        </div>
                    </form>
                </div>

                <!-- Equipment Form (hidden by default) -->
                <div id="equipmentForm" class="hidden bg-white dark:bg-gray-800 p-8 rounded-2xl shadow-xl mb-8 border border-gray-200 dark:border-gray-700">
                    <div class="flex items-center justify-between mb-8">
                        <div>
                            <h3 class="text-3xl font-bold text-gray-800 dark:text-white">Equipment Maintenance Entry</h3>
                            <p class="text-gray-600 dark:text-gray-400 mt-2">Record equipment maintenance and calibration activities</p>
                        </div>
                        <div class="bg-yellow-100 dark:bg-yellow-900 p-4 rounded-xl">
                            <i class="fas fa-tools text-3xl text-yellow-600 dark:text-yellow-400"></i>
                        </div>
                    </div>
                    
                    <form class="grid grid-cols-1 md:grid-cols-2 gap-6">
                        <div class="space-y-2">
                            <label class="block text-sm font-semibold text-gray-700 dark:text-gray-300">Equipment Type *</label>
                            <input type="text" name="equipmentType" required 
                                   class="w-full text-base px-4 py-4 border-2 border-gray-300 dark:border-gray-600 rounded-xl bg-white dark:bg-gray-700 text-gray-800 dark:text-white focus:ring-2 focus:ring-primary focus:border-primary transition-all"
                                   placeholder="e.g., Track Inspection Device">
                        </div>
                        
                        <div class="space-y-2">
                            <label class="block text-sm font-semibold text-gray-700 dark:text-gray-300">Maintenance Type *</label>
                            <select name="maintenanceType" required 
                                    class="w-full text-base px-4 py-4 border-2 border-gray-300 dark:border-gray-600 rounded-xl bg-white dark:bg-gray-700 text-gray-800 dark:text-white focus:ring-2 focus:ring-primary focus:border-primary transition-all">
                                <option value="">Select type</option>
                                <option value="preventive">🔧 Preventive</option>
                                <option value="corrective">🛠️ Corrective</option>
                                <option value="emergency">🚨 Emergency</option>
                                <option value="calibration">📐 Calibration</option>
                            </select>
                        </div>
                        
                        <div class="space-y-2">
                            <label class="block text-sm font-semibold text-gray-700 dark:text-gray-300">Date *</label>
                            <input type="date" name="date" required 
                                   class="w-full text-base px-4 py-4 border-2 border-gray-300 dark:border-gray-600 rounded-xl bg-white dark:bg-gray-700 text-gray-800 dark:text-white focus:ring-2 focus:ring-primary focus:border-primary transition-all">
                        </div>
                        
                        <div class="space-y-2">
                            <label class="block text-sm font-semibold text-gray-700 dark:text-gray-300">Status</label>
                            <select name="status" 
                                    class="w-full text-base px-4 py-4 border-2 border-gray-300 dark:border-gray-600 rounded-xl bg-white dark:bg-gray-700 text-gray-800 dark:text-white focus:ring-2 focus:ring-primary focus:border-primary transition-all">
                                <option value="completed">✅ Completed</option>
                                <option value="in-progress">🔄 In Progress</option>
                                <option value="pending">⏳ Pending</option>
                            </select>
                        </div>
                        
                        <div class="md:col-span-2 space-y-2">
                            <label class="block text-sm font-semibold text-gray-700 dark:text-gray-300">Description *</label>
                            <textarea name="description" rows="4" required 
                                      class="w-full text-base px-4 py-4 border-2 border-gray-300 dark:border-gray-600 rounded-xl bg-white dark:bg-gray-700 text-gray-800 dark:text-white focus:ring-2 focus:ring-primary focus:border-primary transition-all"
                                      placeholder="Describe the equipment maintenance work..."></textarea>
                        </div>
                        
                        <div class="md:col-span-2 space-y-2">
                            <label class="block text-sm font-semibold text-gray-700 dark:text-gray-300">Remarks</label>
                            <textarea name="remark" rows="3" 
                                      class="w-full text-base px-4 py-4 border-2 border-gray-300 dark:border-gray-600 rounded-xl bg-white dark:bg-gray-700 text-gray-800 dark:text-white focus:ring-2 focus:ring-primary focus:border-primary transition-all"
                                      placeholder="Additional notes..."></textarea>
                        </div>
                        
                        <div class="md:col-span-2">
                            <button type="submit" class="w-full bg-gradient-to-r from-yellow-500 to-orange-500 text-white py-4 px-8 rounded-xl text-lg font-bold hover:from-yellow-600 hover:to-orange-600 transition-all shadow-xl">
                                <i class="fas fa-save mr-3"></i>Save Equipment Entry
                            </button>
                        </div>
                    </form>
                </div>

                <!-- Safety Team Forms (simplified for brevity) -->
                <div id="safetyForms" class="hidden space-y-8">
                    <!-- Safety Navigation -->
                    <div class="bg-white dark:bg-gray-800 rounded-2xl shadow-xl border border-gray-200 dark:border-gray-700">
                        <div class="flex flex-wrap border-b border-gray-200 dark:border-gray-700 p-2">
                            <button class="safety-nav px-6 py-4 font-medium text-gray-600 dark:text-gray-300 hover:text-primary active rounded-lg mx-1 transition-all" 
                                    data-safety-tab="incident">
                                <i class="fas fa-exclamation-triangle mr-2"></i>Incident Monitoring
                            </button>
                            <button class="safety-nav px-6 py-4 font-medium text-gray-600 dark:text-gray-300 hover:text-primary rounded-lg mx-1 transition-all" 
                                    data-safety-tab="problem">
                                <i class="fas fa-bug mr-2"></i>Problem Monitoring
                            </button>
                            <button class="safety-nav px-6 py-4 font-medium text-gray-600 dark:text-gray-300 hover:text-primary rounded-lg mx-1 transition-all" 
                                    data-safety-tab="quality">
                                <i class="fas fa-check-circle mr-2"></i>Quality Control
                            </button>
                        </div>
                    </div>

                    <!-- Incident Form -->
                    <div id="incidentForm" class="safety-content bg-white dark:bg-gray-800 p-8 rounded-2xl shadow-xl border border-gray-200 dark:border-gray-700">
                        <div class="flex items-center justify-between mb-8">
                            <div>
                                <h3 class="text-3xl font-bold text-gray-800 dark:text-white">Incident Monitoring</h3>
                                <p class="text-gray-600 dark:text-gray-400 mt-2">Report and track safety incidents</p>
                            </div>
                            <div class="bg-red-100 dark:bg-red-900 p-4 rounded-xl">
                                <i class="fas fa-shield-alt text-3xl text-red-600 dark:text-red-400"></i>
                            </div>
                        </div>
                        
                        <form class="grid grid-cols-1 md:grid-cols-2 gap-6">
                            <div class="space-y-2">
                                <label class="block text-sm font-semibold text-gray-700 dark:text-gray-300">Date *</label>
                                <input type="date" name="date" required 
                                       class="w-full text-base px-4 py-4 border-2 border-gray-300 dark:border-gray-600 rounded-xl bg-white dark:bg-gray-700 text-gray-800 dark:text-white focus:ring-2 focus:ring-red-500 focus:border-red-500 transition-all">
                            </div>
                            
                            <div class="space-y-2">
                                <label class="block text-sm font-semibold text-gray-700 dark:text-gray-300">Train Number *</label>
                                <input type="text" name="trainNumber" required 
                                       class="w-full text-base px-4 py-4 border-2 border-gray-300 dark:border-gray-600 rounded-xl bg-white dark:bg-gray-700 text-gray-800 dark:text-white focus:ring-2 focus:ring-red-500 focus:border-red-500 transition-all"
                                       placeholder="e.g., T001">
                            </div>
                            
                            <div class="space-y-2">
                                <label class="block text-sm font-semibold text-gray-700 dark:text-gray-300">Location (km) *</label>
                                <input type="number" name="location" required 
                                       class="w-full text-base px-4 py-4 border-2 border-gray-300 dark:border-gray-600 rounded-xl bg-white dark:bg-gray-700 text-gray-800 dark:text-white focus:ring-2 focus:ring-red-500 focus:border-red-500 transition-all"
                                       placeholder="Distance from origin">
                            </div>
                            
                            <div class="space-y-2">
                                <label class="block text-sm font-semibold text-gray-700 dark:text-gray-300">Train Captain *</label>
                                <input type="text" name="trainCaptain" required 
                                       class="w-full text-base px-4 py-4 border-2 border-gray-300 dark:border-gray-600 rounded-xl bg-white dark:bg-gray-700 text-gray-800 dark:text-white focus:ring-2 focus:ring-red-500 focus:border-red-500 transition-all"
                                       placeholder="Captain name">
                            </div>
                            
                            <div class="md:col-span-2 space-y-2">
                                <label class="block text-sm font-semibold text-gray-700 dark:text-gray-300">Incident Type *</label>
                                <select name="accidentType" required 
                                        class="w-full text-base px-4 py-4 border-2 border-gray-300 dark:border-gray-600 rounded-xl bg-white dark:bg-gray-700 text-gray-800 dark:text-white focus:ring-2 focus:ring-red-500 focus:border-red-500 transition-all">
                                    <option value="">Select incident type</option>
                                    <option value="derailment">🚂 Derailment</option>
                                    <option value="collision">💥 Collision</option>
                                    <option value="fire">🔥 Fire</option>
                                    <option value="mechanical">⚙️ Mechanical Failure</option>
                                    <option value="signal">🚦 Signal Related</option>
                                    <option value="other">❓ Other</option>
                                </select>
                            </div>
                            
                            <div class="md:col-span-2 space-y-2">
                                <label class="block text-sm font-semibold text-gray-700 dark:text-gray-300">Description</label>
                                <textarea name="description" rows="4" 
                                          class="w-full text-base px-4 py-4 border-2 border-gray-300 dark:border-gray-600 rounded-xl bg-white dark:bg-gray-700 text-gray-800 dark:text-white focus:ring-2 focus:ring-red-500 focus:border-red-500 transition-all"
                                          placeholder="Describe the incident in detail..."></textarea>
                            </div>
                            
                            <div class="md:col-span-2">
                                <button type="submit" class="w-full bg-gradient-to-r from-red-500 to-red-600 text-white py-4 px-8 rounded-xl text-lg font-bold hover:from-red-600 hover:to-red-700 transition-all shadow-xl">
                                    <i class="fas fa-save mr-3"></i>Save Incident Report
                                </button>
                            </div>
                        </form>
                    </div>

                    <!-- Additional safety forms would go here (problem monitoring, quality control) -->
                    <div id="problemForm" class="safety-content hidden">
                        <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl shadow-xl border border-gray-200 dark:border-gray-700">
                            <h3 class="text-2xl font-bold text-gray-800 dark:text-white mb-6">Problem Monitoring Form</h3>
                            <p class="text-gray-600 dark:text-gray-400 mb-4">This form would contain problem monitoring fields...</p>
                        </div>
                    </div>

                    <div id="qualityForm" class="safety-content hidden">
                        <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl shadow-xl border border-gray-200 dark:border-gray-700">
                            <h3 class="text-2xl font-bold text-gray-800 dark:text-white mb-6">Quality Control Form</h3>
                            <p class="text-gray-600 dark:text-gray-400 mb-4">This form would contain quality control fields...</p>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Additional Tasks Tab -->
            <div id="additionalTasks" class="tab-content hidden">
                <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl shadow-xl border border-gray-200 dark:border-gray-700">
                    <div class="flex items-center justify-between mb-8">
                        <div>
                            <h3 class="text-3xl font-bold text-gray-800 dark:text-white">Additional Tasks</h3>
                            <p class="text-gray-600 dark:text-gray-400 mt-2">Record miscellaneous tasks and activities</p>
                        </div>
                        <div class="bg-blue-100 dark:bg-blue-900 p-4 rounded-xl">
                            <i class="fas fa-tasks text-3xl text-blue-600 dark:text-blue-400"></i>
                        </div>
                    </div>
                    
                    <form id="additionalTaskForm" class="space-y-6">
                        <div class="space-y-2">
                            <label class="block text-sm font-semibold text-gray-700 dark:text-gray-300">Date *</label>
                            <input type="date" name="date" required 
                                   class="w-full text-base px-4 py-4 border-2 border-gray-300 dark:border-gray-600 rounded-xl bg-white dark:bg-gray-700 text-gray-800 dark:text-white focus:ring-2 focus:ring-primary focus:border-primary transition-all">
                        </div>
                        
                        <div class="space-y-2">
                            <label class="block text-sm font-semibold text-gray-700 dark:text-gray-300">Task Description *</label>
                            <textarea name="description" rows="5" required 
                                      class="w-full text-base px-4 py-4 border-2 border-gray-300 dark:border-gray-600 rounded-xl bg-white dark:bg-gray-700 text-gray-800 dark:text-white focus:ring-2 focus:ring-primary focus:border-primary transition-all"
                                      placeholder="Describe the additional task performed..."></textarea>
                        </div>
                        
                        <div class="space-y-2">
                            <label class="block text-sm font-semibold text-gray-700 dark:text-gray-300">Remarks</label>
                            <textarea name="remark" rows="3" 
                                      class="w-full text-base px-4 py-4 border-2 border-gray-300 dark:border-gray-600 rounded-xl bg-white dark:bg-gray-700 text-gray-800 dark:text-white focus:ring-2 focus:ring-primary focus:border-primary transition-all"
                                      placeholder="Additional notes or comments..."></textarea>
                        </div>
                        
                        <button type="submit" class="w-full bg-gradient-to-r from-blue-500 to-blue-600 text-white py-4 px-8 rounded-xl text-lg font-bold hover:from-blue-600 hover:to-blue-700 transition-all shadow-xl">
                            <i class="fas fa-save mr-3"></i>Save Additional Task
                        </button>
                    </form>
                </div>
            </div>

            <!-- Reports Tab -->
            <div id="reports" class="tab-content hidden">
                <div class="grid grid-cols-1 lg:grid-cols-2 gap-8">
                    <!-- Report Generation -->
                    <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl shadow-xl border border-gray-200 dark:border-gray-700">
                        <div class="flex items-center justify-between mb-8">
                            <div>
                                <h3 class="text-2xl font-bold text-gray-800 dark:text-white">Generate Reports</h3>
                                <p class="text-gray-600 dark:text-gray-400 mt-2">Export data and analytics</p>
                            </div>
                            <div class="bg-green-100 dark:bg-green-900 p-4 rounded-xl">
                                <i class="fas fa-file-alt text-3xl text-green-600 dark:text-green-400"></i>
                            </div>
                        </div>
                        
                        <div class="space-y-6">
                            <div class="space-y-2">
                                <label class="block text-sm font-semibold text-gray-700 dark:text-gray-300">Report Type</label>
                                <select id="reportType" class="w-full text-base px-4 py-4 border-2 border-gray-300 dark:border-gray-600 rounded-xl bg-white dark:bg-gray-700 text-gray-800 dark:text-white focus:ring-2 focus:ring-primary transition-all">
                                    <option value="maintenance">📊 Maintenance Report</option>
                                    <option value="incidents">🚨 Incident Report</option>
                                    <option value="equipment">⚙️ Equipment Report</option>
                                    <option value="summary">📋 Summary Report</option>
                                </select>
                            </div>
                            
                            <div class="space-y-2">
                                <label class="block text-sm font-semibold text-gray-700 dark:text-gray-300">Date Range</label>
                                <div class="grid grid-cols-2 gap-4">
                                    <input type="date" id="startDate" class="text-base px-4 py-4 border-2 border-gray-300 dark:border-gray-600 rounded-xl bg-white dark:bg-gray-700 text-gray-800 dark:text-white focus:ring-2 focus:ring-primary transition-all">
                                    <input type="date" id="endDate" class="text-base px-4 py-4 border-2 border-gray-300 dark:border-gray-600 rounded-xl bg-white dark:bg-gray-700 text-gray-800 dark:text-white focus:ring-2 focus:ring-primary transition-all">
                                </div>
                            </div>
                            
                            <button id="generateReport" class="w-full bg-primary text-white py-4 px-6 rounded-xl text-lg font-semibold hover:bg-blue-600 transition-all shadow-lg">
                                <i class="fas fa-file-alt mr-3"></i>Generate Report
                            </button>
                            
                            <button id="exportReport" class="w-full bg-success text-white py-4 px-6 rounded-xl text-lg font-semibold hover:bg-green-600 transition-all shadow-lg">
                                <i class="fas fa-download mr-3"></i>Export to Excel
                            </button>
                        </div>
                    </div>
                    
                    <!-- Team Analytics Chart -->
                    <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl shadow-xl border border-gray-200 dark:border-gray-700">
                        <h3 class="text-2xl font-bold text-gray-800 dark:text-white mb-6">Team Performance</h3>
                        <div class="chart-container">
                            <canvas id="teamChart"></canvas>
                        </div>
                    </div>
                </div>
                
                <!-- Data Table -->
                <div class="mt-8 bg-white dark:bg-gray-800 p-8 rounded-2xl shadow-xl border border-gray-200 dark:border-gray-700">
                    <h3 class="text-2xl font-bold text-gray-800 dark:text-white mb-6">Recent Entries</h3>
                    <div class="overflow-x-auto">
                        <table id="dataTable" class="w-full table-auto">
                            <thead class="bg-gray-50 dark:bg-gray-700">
                                <tr>
                                    <th class="px-6 py-4 text-left text-xs font-bold text-gray-500 dark:text-gray-300 uppercase tracking-wider">Date</th>
                                    <th class="px-6 py-4 text-left text-xs font-bold text-gray-500 dark:text-gray-300 uppercase tracking-wider">Team</th>
                                    <th class="px-6 py-4 text-left text-xs font-bold text-gray-500 dark:text-gray-300 uppercase tracking-wider">Unit/Number</th>
                                    <th class="px-6 py-4 text-left text-xs font-bold text-gray-500 dark:text-gray-300 uppercase tracking-wider">Status</th>
                                    <th class="px-6 py-4 text-left text-xs font-bold text-gray-500 dark:text-gray-300 uppercase tracking-wider">Actions</th>
                                </tr>
                            </thead>
                            <tbody id="tableBody" class="bg-white dark:bg-gray-800 divide-y divide-gray-200 dark:divide-gray-700">
                                <!-- Dynamic content -->
                            </tbody>
                        </table>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <!-- Login Modal -->
    <div id="loginModal" class="hidden fixed inset-0 bg-black bg-opacity-70 flex items-center justify-center z-50 backdrop-blur-sm">
        <div class="bg-white dark:bg-gray-800 p-10 rounded-3xl shadow-2xl max-w-lg w-full mx-4 border border-gray-200 dark:border-gray-700">
            <div class="text-center mb-8">
                <div class="bg-primary bg-opacity-10 p-6 rounded-full w-20 h-20 mx-auto mb-4 flex items-center justify-center">
                    <i class="fas fa-train text-primary text-3xl"></i>
                </div>
                <h2 class="text-3xl font-bold text-gray-800 dark:text-white">Railway Management Login</h2>
                <p class="text-gray-600 dark:text-gray-300 mt-3 text-lg">Secure access to the system</p>
            </div>
            
            <form id="loginForm">
                <div class="space-y-6">
                    <div class="space-y-2">
                        <label class="block text-sm font-semibold text-gray-700 dark:text-gray-300">Username</label>
                        <input type="text" id="loginUsername" required 
                               class="w-full text-base px-4 py-4 border-2 border-gray-300 dark:border-gray-600 rounded-xl bg-white dark:bg-gray-700 text-gray-800 dark:text-white focus:ring-2 focus:ring-primary focus:border-primary transition-all"
                               placeholder="Enter your username">
                    </div>
                    
                    <div class="space-y-2">
                        <label class="block text-sm font-semibold text-gray-700 dark:text-gray-300">Password</label>
                        <input type="password" id="loginPassword" required 
                               class="w-full text-base px-4 py-4 border-2 border-gray-300 dark:border-gray-600 rounded-xl bg-white dark:bg-gray-700 text-gray-800 dark:text-white focus:ring-2 focus:ring-primary focus:border-primary transition-all"
                               placeholder="Enter your password">
                    </div>
                    
                    <div id="loginError" class="hidden bg-red-100 dark:bg-red-900 border-2 border-red-300 dark:border-red-700 text-red-700 dark:text-red-300 px-4 py-3 rounded-xl">
                        <i class="fas fa-exclamation-triangle mr-2"></i>Invalid username or password
                    </div>
                    
                    <button type="submit" class="w-full bg-gradient-to-r from-primary to-secondary text-white py-4 px-6 rounded-xl text-lg font-bold hover:from-blue-600 hover:to-purple-600 transition-all shadow-xl">
                        <i class="fas fa-sign-in-alt mr-3"></i>Sign In
                    </button>
                </div>
            </form>
            
            <div class="mt-8 p-6 bg-gray-50 dark:bg-gray-700 rounded-2xl">
                <h4 class="font-bold text-gray-800 dark:text-white mb-4">💡 Demo Accounts:</h4>
                <div class="text-sm text-gray-600 dark:text-gray-300 space-y-2">
                    <div class="flex justify-between"><span><strong>System Admin:</strong></span> <code>admin / admin123</code></div>
                    <div class="flex justify-between"><span><strong>Wagon Team:</strong></span> <code>wagon_admin / wagon123</code></div>
                    <div class="flex justify-between"><span><strong>Locomotive:</strong></span> <code>loco_admin / loco123</code></div>
                    <div class="flex justify-between"><span><strong>Coach Team:</strong></span> <code>coach_admin / coach123</code></div>
                    <div class="flex justify-between"><span><strong>Equipment:</strong></span> <code>equip_admin / equip123</code></div>
                    <div class="flex justify-between"><span><strong>Safety Team:</strong></span> <code>safety_admin / safety123</code></div>
                    <div class="flex justify-between"><span><strong>Viewer:</strong></span> <code>viewer / viewer123</code></div>
                </div>
            </div>
        </div>
    </div>

    <!-- Custom Modal -->
    <div id="customModal" class="hidden fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
        <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl shadow-2xl max-w-md w-full mx-4 border border-gray-200 dark:border-gray-700">
            <h3 id="modalTitle" class="text-xl font-bold text-gray-800 dark:text-white mb-4"></h3>
            <p id="modalMessage" class="text-gray-600 dark:text-gray-300 mb-8"></p>
            <div class="flex justify-end space-x-4">
                <button id="modalCancel" class="px-6 py-3 text-gray-600 dark:text-gray-400 hover:bg-gray-100 dark:hover:bg-gray-700 rounded-xl transition-all">Cancel</button>
                <button id="modalConfirm" class="px-6 py-3 bg-primary text-white hover:bg-blue-600 rounded-xl transition-all">Confirm</button>
            </div>
        </div>
    </div>

    <!-- Success Toast -->
    <div id="successToast" class="hidden fixed top-4 right-4 bg-success text-white p-6 rounded-xl shadow-2xl z-50 data-entry-success">
        <div class="flex items-center">
            <i class="fas fa-check-circle text-2xl mr-3"></i>
            <div>
                <h4 class="font-bold">Success!</h4>
                <p id="toastMessage">Entry saved successfully</p>
            </div>
        </div>
    </div>

    <script>
        // Dark mode handling
        if (window.matchMedia && window.matchMedia('(prefers-color-scheme: dark)').matches) {
            document.documentElement.classList.add('dark');
        }
        window.matchMedia('(prefers-color-scheme: dark)').addEventListener('change', event => {
            if (event.matches) {
                document.documentElement.classList.add('dark');
            } else {
                document.documentElement.classList.remove('dark');
            }
        });

        // Application state
        let currentTeam = '';
        let currentUser = { username: null, role: null, team: null, loggedIn: false };
        let charts = {};
        let data = {
            wagon: [],
            locomotive: [],
            coach: [],
            equipment: [],
            incidents: [],
            problems: [],
            qualityControl: [],
            additionalTasks: []
        };

        // User database (in production this would be in a secure backend)
        const users = {
            'admin': { password: 'admin123', role: 'admin', team: 'all', name: 'System Administrator' },
            'wagon_admin': { password: 'wagon123', role: 'admin', team: 'wagon', name: 'Wagon Team Admin' },
            'loco_admin': { password: 'loco123', role: 'admin', team: 'locomotive', name: 'Locomotive Team Admin' },
            'coach_admin': { password: 'coach123', role: 'admin', team: 'coach', name: 'Coach Team Admin' },
            'equip_admin': { password: 'equip123', role: 'admin', team: 'equipment', name: 'Equipment Team Admin' },
            'safety_admin': { password: 'safety123', role: 'admin', team: 'safety', name: 'Safety Team Admin' },
            'viewer': { password: 'viewer123', role: 'viewer', team: 'all', name: 'General Viewer' }
        };

        // Team configurations
        const teamConfig = {
            wagon: { icon: '🚂', title: 'Wagon Team', description: 'Freight wagon maintenance and operations' },
            locomotive: { icon: '🚆', title: 'Locomotive Team', description: 'Engine maintenance and repair services' },
            coach: { icon: '🚌', title: 'Coach Team', description: 'Passenger coach maintenance and comfort' },
            equipment: { icon: '⚙️', title: 'Equipment Team', description: 'Technical equipment and calibration' },
            safety: { icon: '🛡️', title: 'Safety & Quality Team', description: 'Safety monitoring and quality assurance' }
        };

        // Initialize application
        function initializeApp() {
            // Hide loading screen
            setTimeout(() => {
                document.getElementById('loadingScreen').style.display = 'none';
            }, 2000);

            // Load data
            loadData();
            
            // Initialize charts
            initializeCharts();
            updateStatistics();
            updateLastUpdated();
            
            // Set up event listeners
            setupEventListeners();
            
            // Set default tab
            switchTab('dataEntry');
            
            // Check authentication
            checkAuthenticationState();
        }

        // Initialize data (in-memory storage for sandbox environment)
        function loadData() {
            addSampleData();
        }

        // Save data (in-memory only due to sandbox restrictions)
        function saveData() {
            console.log('Data saved in memory:', Object.keys(data).map(key => `${key}: ${data[key].length} entries`).join(', '));
        }

        // Add sample data
        function addSampleData() {
            data.wagon = [
                { id: 1, number: "WAG001", maintenanceType: "preventive", description: "Routine brake inspection and maintenance", fault: "Worn brake pads detected", sparePart: "Brake pads set (4 units)", status: "completed", startDate: "2024-01-15", finishDate: "2024-01-16", remark: "Completed successfully, next service due in 6 months", timestamp: new Date(2024, 0, 16).toISOString(), team: "wagon" },
                { id: 2, number: "WAG002", maintenanceType: "corrective", description: "Wheel bearing replacement", fault: "Noisy bearing detected during inspection", sparePart: "Wheel bearing assembly", status: "in-progress", startDate: "2024-01-18", finishDate: "", remark: "Parts ordered, work to continue tomorrow", timestamp: new Date(2024, 0, 18).toISOString(), team: "wagon" },
                { id: 5, number: "WAG003", maintenanceType: "emergency", description: "Emergency brake system repair", fault: "Complete brake failure", sparePart: "Complete brake system", status: "completed", startDate: "2024-01-20", finishDate: "2024-01-21", remark: "Emergency repair completed successfully", timestamp: new Date(2024, 0, 21).toISOString(), team: "wagon" }
            ];
            
            data.locomotive = [
                { id: 3, number: "LOC001", maintenanceType: "routine", description: "Engine oil change and filter replacement", fault: "", sparePart: "Engine oil (50L), Oil filter", status: "completed", startDate: "2024-01-10", finishDate: "2024-01-10", remark: "Regular maintenance completed as scheduled", timestamp: new Date(2024, 0, 10).toISOString(), team: "locomotive" },
                { id: 6, number: "LOC002", maintenanceType: "corrective", description: "Turbo charger repair and calibration", fault: "Reduced power output, unusual noise", sparePart: "Turbo charger rebuild kit", status: "pending", startDate: "2024-01-22", finishDate: "", remark: "Awaiting specialist parts delivery from supplier", timestamp: new Date(2024, 0, 22).toISOString(), team: "locomotive" }
            ];
            
            data.coach = [
                { id: 7, number: "COA001", maintenanceType: "preventive", description: "Air conditioning system service", fault: "Poor cooling performance", sparePart: "AC filters, refrigerant", status: "completed", startDate: "2024-01-12", finishDate: "2024-01-13", remark: "Service completed, cooling performance restored", timestamp: new Date(2024, 0, 13).toISOString(), team: "coach" },
                { id: 8, number: "COA002", maintenanceType: "routine", description: "Interior cleaning and safety inspection", fault: "", sparePart: "", status: "in-progress", startDate: "2024-01-23", finishDate: "", remark: "Deep cleaning in progress, safety check scheduled", timestamp: new Date(2024, 0, 23).toISOString(), team: "coach" }
            ];
            
            data.equipment = [
                { id: 9, equipmentType: "Track Inspection Device", maintenanceType: "calibration", date: "2024-01-15", description: "Annual calibration of ultrasonic measurement sensors", remark: "Calibration completed successfully, accuracy verified", timestamp: new Date(2024, 0, 15).toISOString(), team: "equipment", status: "completed" }
            ];
            
            data.incidents = [
                { id: 4, date: "2024-01-20", trainNumber: "T001", location: 45, trainCaptain: "John Smith", accidentType: "mechanical", description: "Minor mechanical failure caused 30-minute delay", timestamp: new Date(2024, 0, 20).toISOString(), team: "safety" },
                { id: 10, date: "2024-01-18", trainNumber: "T002", location: 67, trainCaptain: "Ahmed Hassan", accidentType: "signal", description: "Signal malfunction caused temporary stop", timestamp: new Date(2024, 0, 18).toISOString(), team: "safety" }
            ];
            
            saveData();
        }

        // Custom modal functions
        function showModal(title, message, onConfirm) {
            const modal = document.getElementById('customModal');
            const modalTitle = document.getElementById('modalTitle');
            const modalMessage = document.getElementById('modalMessage');
            const modalCancel = document.getElementById('modalCancel');
            const modalConfirm = document.getElementById('modalConfirm');
            
            modalTitle.textContent = title;
            modalMessage.textContent = message;
            
            modal.classList.remove('hidden');
            
            const handleConfirm = () => {
                modal.classList.add('hidden');
                if (onConfirm) onConfirm();
                cleanup();
            };
            
            const handleCancel = () => {
                modal.classList.add('hidden');
                cleanup();
            };
            
            const cleanup = () => {
                modalConfirm.removeEventListener('click', handleConfirm);
                modalCancel.removeEventListener('click', handleCancel);
            };
            
            modalConfirm.addEventListener('click', handleConfirm);
            modalCancel.addEventListener('click', handleCancel);
        }

        function showAlert(message) {
            showModal('System Notification', message, null);
        }

        function showConfirm(message, onConfirm) {
            showModal('Confirmation Required', message, onConfirm);
        }

        function showSuccessToast(message) {
            const toast = document.getElementById('successToast');
            const toastMessage = document.getElementById('toastMessage');
            
            toastMessage.textContent = message;
            toast.classList.remove('hidden');
            
            setTimeout(() => {
                toast.classList.add('hidden');
            }, 4000);
        }

        // Update last updated timestamp
        function updateLastUpdated() {
            document.getElementById('lastUpdated').textContent = 
                `Last updated: ${new Date().toLocaleString()}`;
        }

        // Quick action functions
        function quickAction(team) {
            if (!currentUser.loggedIn) {
                showAlert('Please login to access team functions.');
                return;
            }
            
            if (currentUser.team !== 'all' && currentUser.team !== team) {
                showAlert('You do not have access to this team.');
                return;
            }
            
            document.getElementById('teamSelector').value = team;
            showTeamSection(team);
        }

        function exportAllData() {
            if (!currentUser.loggedIn) {
                showAlert('Please login to export data.');
                return;
            }
            
            exportReport();
        }

        // Initialize charts
        function initializeCharts() {
            // Maintenance Status Chart
            const maintenanceCtx = document.getElementById('maintenanceChart').getContext('2d');
            charts.maintenance = new Chart(maintenanceCtx, {
                type: 'doughnut',
                data: {
                    labels: ['Completed', 'In Progress', 'Pending', 'Cancelled'],
                    datasets: [{
                        data: [0, 0, 0, 0],
                        backgroundColor: ['#10B981', '#F59E0B', '#EF4444', '#6B7280'],
                        borderWidth: 3,
                        borderColor: document.documentElement.classList.contains('dark') ? '#374151' : '#FFFFFF'
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        legend: {
                            position: 'bottom',
                            labels: {
                                color: document.documentElement.classList.contains('dark') ? '#D1D5DB' : '#374151',
                                font: { size: 12, weight: 'bold' },
                                padding: 20
                            }
                        }
                    }
                }
            });

            // Incident Chart
            const incidentCtx = document.getElementById('incidentChart').getContext('2d');
            charts.incident = new Chart(incidentCtx, {
                type: 'line',
                data: {
                    labels: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun'],
                    datasets: [{
                        label: 'Safety Incidents',
                        data: [2, 1, 3, 1, 2, 1],
                        borderColor: '#EF4444',
                        backgroundColor: 'rgba(239, 68, 68, 0.1)',
                        tension: 0.4,
                        fill: true,
                        borderWidth: 3,
                        pointBackgroundColor: '#EF4444',
                        pointBorderColor: '#FFFFFF',
                        pointBorderWidth: 2,
                        pointRadius: 6
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        legend: {
                            labels: {
                                color: document.documentElement.classList.contains('dark') ? '#D1D5DB' : '#374151',
                                font: { weight: 'bold' }
                            }
                        }
                    },
                    scales: {
                        y: {
                            beginAtZero: true,
                            ticks: {
                                color: document.documentElement.classList.contains('dark') ? '#D1D5DB' : '#374151',
                                font: { weight: 'bold' }
                            },
                            grid: {
                                color: document.documentElement.classList.contains('dark') ? '#374151' : '#E5E7EB'
                            }
                        },
                        x: {
                            ticks: {
                                color: document.documentElement.classList.contains('dark') ? '#D1D5DB' : '#374151',
                                font: { weight: 'bold' }
                            },
                            grid: {
                                color: document.documentElement.classList.contains('dark') ? '#374151' : '#E5E7EB'
                            }
                        }
                    }
                }
            });

            // Initial chart update
            setTimeout(() => updateChartsWithRealData(), 500);
        }

        // Team-specific chart
        function initializeTeamChart() {
            const teamCtx = document.getElementById('teamChart');
            if (!teamCtx) return;
            
            if (charts.team) {
                charts.team.destroy();
            }
            
            const teamData = getTeamChartData();
            
            charts.team = new Chart(teamCtx.getContext('2d'), {
                type: 'bar',
                data: {
                    labels: ['This Week', 'Last Week', 'This Month', 'Last Month'],
                    datasets: [{
                        label: 'Maintenance Activities',
                        data: teamData,
                        backgroundColor: '#5D5CDE',
                        borderRadius: 8,
                        borderSkipped: false,
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        legend: {
                            labels: {
                                color: document.documentElement.classList.contains('dark') ? '#D1D5DB' : '#374151',
                                font: { weight: 'bold' }
                            }
                        }
                    },
                    scales: {
                        y: {
                            beginAtZero: true,
                            ticks: {
                                color: document.documentElement.classList.contains('dark') ? '#D1D5DB' : '#374151',
                                font: { weight: 'bold' }
                            },
                            grid: {
                                color: document.documentElement.classList.contains('dark') ? '#374151' : '#E5E7EB'
                            }
                        },
                        x: {
                            ticks: {
                                color: document.documentElement.classList.contains('dark') ? '#D1D5DB' : '#374151',
                                font: { weight: 'bold' }
                            },
                            grid: {
                                color: document.documentElement.classList.contains('dark') ? '#374151' : '#E5E7EB'
                            }
                        }
                    }
                }
            });
        }

        function getTeamChartData() {
            let teamData = [0, 0, 0, 0];
            if (currentTeam && data[currentTeam]) {
                const currentTeamData = data[currentTeam];
                teamData = [
                    Math.floor(currentTeamData.length * 0.2), // This week
                    Math.floor(currentTeamData.length * 0.3), // Last week  
                    currentTeamData.length, // This month
                    Math.floor(currentTeamData.length * 0.8)  // Last month
                ];
            }
            return teamData;
        }

        // Update statistics
        function updateStatistics() {
            document.getElementById('totalWagons').textContent = data.wagon ? data.wagon.length : 0;
            document.getElementById('totalLocomotives').textContent = data.locomotive ? data.locomotive.length : 0;
            document.getElementById('totalCoaches').textContent = data.coach ? data.coach.length : 0;
            document.getElementById('totalIncidents').textContent = data.incidents ? data.incidents.length : 0;
            updateLastUpdated();
        }

        // Update charts with real data
        function updateChartsWithRealData() {
            if (!charts.maintenance) return;
            
            // Calculate real maintenance status
            let completed = 0, inProgress = 0, pending = 0, cancelled = 0;
            
            Object.keys(data).forEach(team => {
                if (data[team] && Array.isArray(data[team])) {
                    data[team].forEach(entry => {
                        switch (entry.status?.toLowerCase()) {
                            case 'completed':
                                completed++;
                                break;
                            case 'in-progress':
                                inProgress++;
                                break;
                            case 'pending':
                                pending++;
                                break;
                            case 'cancelled':
                                cancelled++;
                                break;
                        }
                    });
                }
            });
            
            // Update maintenance chart with real data
            charts.maintenance.data.datasets[0].data = [completed, inProgress, pending, cancelled];
            charts.maintenance.update();
        }

        // Authentication functions
        function showLoginModal() {
            document.getElementById('loginModal').classList.remove('hidden');
            document.getElementById('loginError').classList.add('hidden');
            document.getElementById('loginUsername').focus();
        }

        function hideLoginModal() {
            document.getElementById('loginModal').classList.add('hidden');
            document.getElementById('loginForm').reset();
        }

        function authenticateUser() {
            const username = document.getElementById('loginUsername').value;
            const password = document.getElementById('loginPassword').value;
            const errorDiv = document.getElementById('loginError');

            // Check credentials
            if (users[username] && users[username].password === password) {
                // Successful login
                currentUser = {
                    username: username,
                    role: users[username].role,
                    team: users[username].team,
                    name: users[username].name,
                    loggedIn: true
                };

                hideLoginModal();
                updateUIAfterLogin();
                showSuccessToast(`Welcome back, ${currentUser.name}!`);
            } else {
                // Failed login
                errorDiv.classList.remove('hidden');
                document.getElementById('loginPassword').value = '';
                document.getElementById('loginPassword').focus();
            }
        }

        function logout() {
            showConfirm('Are you sure you want to logout?', () => {
                currentUser = { username: null, role: null, team: null, loggedIn: false };
                updateUIAfterLogout();
                showSuccessToast('You have been logged out successfully.');
            });
        }

        function updateUIAfterLogin() {
            const loginBtn = document.getElementById('loginBtn');
            const teamSelector = document.getElementById('teamSelector');
            const userInfo = document.getElementById('userInfo');
            const userName = document.getElementById('userName');
            const userRole = document.getElementById('userRole');

            // Update login button
            loginBtn.innerHTML = `<i class="fas fa-sign-out-alt mr-2"></i>Logout`;
            loginBtn.className = "px-6 py-3 bg-red-500 text-white rounded-xl hover:bg-red-600 transition-all font-medium shadow-lg";

            // Show user info
            userInfo.classList.remove('hidden');
            userName.textContent = currentUser.name;
            userRole.textContent = `${currentUser.role.charAt(0).toUpperCase() + currentUser.role.slice(1)} - ${teamConfig[currentUser.team]?.title || 'All Teams'}`;

            // Restrict team access based on user team
            if (currentUser.team !== 'all') {
                // Hide teams not accessible to this user
                Array.from(teamSelector.options).forEach(option => {
                    if (option.value && option.value !== currentUser.team) {
                        option.style.display = 'none';
                    }
                });
                
                // Auto-select user's team
                teamSelector.value = currentUser.team;
                showTeamSection(currentUser.team);
            }

            updateUIPermissions();
        }

        function updateUIAfterLogout() {
            const loginBtn = document.getElementById('loginBtn');
            const teamSelector = document.getElementById('teamSelector');
            const userInfo = document.getElementById('userInfo');

            // Reset login button
            loginBtn.innerHTML = `<i class="fas fa-sign-in-alt mr-2"></i>Login`;
            loginBtn.className = "px-6 py-3 bg-primary text-white rounded-xl hover:bg-blue-600 transition-all font-medium shadow-lg";

            // Hide user info
            userInfo.classList.add('hidden');

            // Show all team options
            Array.from(teamSelector.options).forEach(option => {
                option.style.display = 'block';
            });

            // Reset team selection
            teamSelector.value = '';
            showDashboard();

            updateUIPermissions();
        }

        function checkAuthenticationState() {
            // For demo purposes, start with no authentication
            updateUIAfterLogout();
        }

        // Navigation handling
        function showTeamSection(team) {
            if (!currentUser.loggedIn) {
                showAlert('Please login to access team functions.');
                document.getElementById('teamSelector').value = '';
                return;
            }

            if (currentUser.team !== 'all' && currentUser.team !== team) {
                showAlert('You do not have access to this team.');
                document.getElementById('teamSelector').value = '';
                return;
            }

            currentTeam = team;
            document.getElementById('dashboardSection').style.display = 'none';
            document.getElementById('teamSection').classList.remove('hidden');
            
            // Update team header
            const config = teamConfig[team];
            if (config) {
                document.getElementById('teamIcon').textContent = config.icon;
                document.getElementById('teamTitle').textContent = config.title;
                document.getElementById('teamDescription').textContent = config.description;
            }
            
            // Update form titles
            const formTitle = document.getElementById('formTitle');
            if (formTitle) {
                formTitle.textContent = team.charAt(0).toUpperCase() + team.slice(1);
            }
            
            // Show appropriate forms
            const standardForm = document.getElementById('standardForm');
            const equipmentForm = document.getElementById('equipmentForm');
            const safetyForms = document.getElementById('safetyForms');
            
            if (team === 'equipment') {
                standardForm.style.display = 'none';
                equipmentForm.classList.remove('hidden');
                safetyForms.classList.add('hidden');
            } else if (team === 'safety') {
                standardForm.style.display = 'none';
                equipmentForm.classList.add('hidden');
                safetyForms.classList.remove('hidden');
            } else {
                standardForm.style.display = 'block';
                equipmentForm.classList.add('hidden');
                safetyForms.classList.add('hidden');
            }
            
            // Initialize team chart
            setTimeout(() => initializeTeamChart(), 100);
            updateDataTable();
        }

        function showDashboard() {
            document.getElementById('dashboardSection').style.display = 'block';
            document.getElementById('teamSection').classList.add('hidden');
            currentTeam = '';
        }

        // Tab handling
        function switchTab(tabName) {
            // Hide all tab contents
            document.querySelectorAll('.tab-content').forEach(tab => {
                tab.classList.add('hidden');
            });
            
            // Remove active class from all nav items
            document.querySelectorAll('.nav-item').forEach(item => {
                item.classList.remove('active');
            });
            
            // Show selected tab
            const targetTab = document.getElementById(tabName);
            if (targetTab) {
                targetTab.classList.remove('hidden');
            }
            
            // Add active class to clicked nav item
            const activeNav = document.querySelector(`[data-tab="${tabName}"]`);
            if (activeNav) {
                activeNav.classList.add('active');
            }
            
            // Initialize chart if on reports tab
            if (tabName === 'reports') {
                setTimeout(() => initializeTeamChart(), 100);
            }
        }

        // Safety team navigation
        function switchSafetyTab(tabName) {
            // Hide all safety contents
            document.querySelectorAll('.safety-content').forEach(content => {
                content.classList.add('hidden');
            });
            
            // Remove active class from safety nav
            document.querySelectorAll('.safety-nav').forEach(nav => {
                nav.classList.remove('active');
            });
            
            // Show selected content
            const targetContent = document.getElementById(tabName + 'Form');
            if (targetContent) {
                targetContent.classList.remove('hidden');
            }
            
            // Add active class
            const activeNav = document.querySelector(`[data-safety-tab="${tabName}"]`);
            if (activeNav) {
                activeNav.classList.add('active');
            }
        }

        // Form handling
        function handleFormSubmission(form, dataType) {
            if (!currentUser.loggedIn) {
                showAlert('Please login to submit data.');
                return;
            }
            
            if (currentUser.role !== 'admin') {
                showAlert('You need admin access to submit data.');
                return;
            }

            const formData = new FormData(form);
            const entry = {};
            
            for (let [key, value] of formData.entries()) {
                entry[key] = value;
            }
            
            entry.id = Date.now();
            entry.timestamp = new Date().toISOString();
            entry.team = currentTeam;
            entry.createdBy = currentUser.username;
            
            // Add to data array
            if (!data[dataType]) {
                data[dataType] = [];
            }
            data[dataType].push(entry);
            
            // Save data
            saveData();
            
            // Update statistics and UI
            updateStatistics();
            updateDataTable();
            updateChartsWithRealData();
            
            // Show success message
            showSuccessToast(`${dataType.charAt(0).toUpperCase() + dataType.slice(1)} entry saved successfully! Entry ID: ${entry.id}`);
            
            // Reset form
            form.reset();
        }

        // Update data table
        function updateDataTable() {
            const tableBody = document.getElementById('tableBody');
            if (!tableBody) return;
            
            tableBody.innerHTML = '';
            
            // Combine relevant data based on current team
            let relevantData = [];
            if (currentTeam === 'wagon') {
                relevantData = data.wagon || [];
            } else if (currentTeam === 'locomotive') {
                relevantData = data.locomotive || [];
            } else if (currentTeam === 'coach') {
                relevantData = data.coach || [];
            } else if (currentTeam === 'equipment') {
                relevantData = data.equipment || [];
            } else if (currentTeam === 'safety') {
                relevantData = [...(data.incidents || []), ...(data.problems || []), ...(data.qualityControl || [])];
            } else {
                // Show all data for dashboard
                relevantData = [
                    ...(data.wagon || []),
                    ...(data.locomotive || []),
                    ...(data.coach || []),
                    ...(data.equipment || []),
                    ...(data.incidents || []),
                    ...(data.problems || [])
                ];
            }
            
            // Sort by timestamp (newest first)
            relevantData.sort((a, b) => new Date(b.timestamp) - new Date(a.timestamp));
            
            // Show only last 10 entries
            relevantData.slice(0, 10).forEach(entry => {
                const row = document.createElement('tr');
                row.className = 'hover:bg-gray-50 dark:hover:bg-gray-700 transition-colors';
                row.innerHTML = `
                    <td class="px-6 py-4 text-sm font-medium text-gray-800 dark:text-gray-200">
                        ${new Date(entry.timestamp).toLocaleDateString()}
                    </td>
                    <td class="px-6 py-4 text-sm text-gray-800 dark:text-gray-200">
                        <span class="inline-flex items-center px-2 py-1 rounded-full text-xs font-medium bg-blue-100 text-blue-800 dark:bg-blue-900 dark:text-blue-200">
                            ${teamConfig[entry.team]?.icon || '📋'} ${entry.team || 'N/A'}
                        </span>
                    </td>
                    <td class="px-6 py-4 text-sm font-medium text-gray-800 dark:text-gray-200">
                        ${entry.number || entry.trainNumber || entry.equipmentType || 'N/A'}
                    </td>
                    <td class="px-6 py-4 text-sm">
                        <span class="inline-flex items-center px-3 py-1 rounded-full text-xs font-medium ${getStatusColor(entry.status)}">
                            ${getStatusIcon(entry.status)} ${entry.status || 'N/A'}
                        </span>
                    </td>
                    <td class="px-6 py-4 text-sm">
                        <div class="flex space-x-2">
                            <button onclick="viewEntry('${entry.id}')" class="text-blue-600 hover:text-blue-800 p-2 rounded-lg hover:bg-blue-50 dark:hover:bg-blue-900 transition-all">
                                <i class="fas fa-eye"></i>
                            </button>
                            <button onclick="editEntry('${entry.id}')" class="text-green-600 hover:text-green-800 p-2 rounded-lg hover:bg-green-50 dark:hover:bg-green-900 transition-all">
                                <i class="fas fa-edit"></i>
                            </button>
                            <button onclick="deleteEntry('${entry.id}')" class="text-red-600 hover:text-red-800 p-2 rounded-lg hover:bg-red-50 dark:hover:bg-red-900 transition-all">
                                <i class="fas fa-trash"></i>
                            </button>
                        </div>
                    </td>
                `;
                tableBody.appendChild(row);
            });
        }

        function getStatusColor(status) {
            switch (status?.toLowerCase()) {
                case 'completed':
                case 'approved':
                    return 'bg-green-100 text-green-800 dark:bg-green-800 dark:text-green-100';
                case 'in-progress':
                    return 'bg-yellow-100 text-yellow-800 dark:bg-yellow-800 dark:text-yellow-100';
                case 'pending':
                    return 'bg-blue-100 text-blue-800 dark:bg-blue-800 dark:text-blue-100';
                case 'cancelled':
                case 'rejected':
                    return 'bg-red-100 text-red-800 dark:bg-red-800 dark:text-red-100';
                default:
                    return 'bg-gray-100 text-gray-800 dark:bg-gray-800 dark:text-gray-100';
            }
        }

        function getStatusIcon(status) {
            switch (status?.toLowerCase()) {
                case 'completed':
                case 'approved':
                    return '✅';
                case 'in-progress':
                    return '🔄';
                case 'pending':
                    return '⏳';
                case 'cancelled':
                case 'rejected':
                    return '❌';
                default:
                    return '📋';
            }
        }

        // Entry management
        function viewEntry(id) {
            showAlert('View entry functionality would be implemented here.');
        }

        function editEntry(id) {
            showAlert('Edit entry functionality would be implemented here.');
        }

        function deleteEntry(id) {
            showConfirm('Are you sure you want to delete this entry?', () => {
                // Find and remove entry from all data arrays
                Object.keys(data).forEach(key => {
                    if (data[key]) {
                        data[key] = data[key].filter(entry => entry.id != id);
                    }
                });
                
                updateStatistics();
                updateDataTable();
                updateChartsWithRealData();
                showSuccessToast('Entry deleted successfully!');
            });
        }

        // Report generation
        function generateReport() {
            const reportType = document.getElementById('reportType').value;
            const startDate = document.getElementById('startDate').value;
            const endDate = document.getElementById('endDate').value;
            
            if (!startDate || !endDate) {
                showAlert('Please select both start and end dates.');
                return;
            }
            
            showSuccessToast(`${reportType.charAt(0).toUpperCase() + reportType.slice(1)} report generated for ${startDate} to ${endDate}`);
        }

        function exportReport() {
            // Create CSV content
            let csvContent = "data:text/csv;charset=utf-8,";
            csvContent += "Date,Team,Number,Type,Status,Description,Created By\n";
            
            // Add data rows
            Object.keys(data).forEach(team => {
                if (data[team]) {
                    data[team].forEach(entry => {
                        const row = [
                            new Date(entry.timestamp).toLocaleDateString(),
                            entry.team || team,
                            entry.number || entry.trainNumber || entry.equipmentType || '',
                            entry.maintenanceType || entry.accidentType || '',
                            entry.status || '',
                            (entry.description || '').replace(/,/g, ';'),
                            entry.createdBy || 'System'
                        ].join(',');
                        csvContent += row + "\n";
                    });
                }
            });
            
            // Download file
            const encodedUri = encodeURI(csvContent);
            const link = document.createElement("a");
            link.setAttribute("href", encodedUri);
            link.setAttribute("download", `railway_report_${new Date().toISOString().split('T')[0]}.csv`);
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
            
            showSuccessToast('Report exported successfully!');
        }

        function updateUIPermissions() {
            const isAdmin = currentUser.loggedIn && currentUser.role === 'admin';
            const submitButtons = document.querySelectorAll('button[type="submit"]');
            
            submitButtons.forEach(button => {
                if (isAdmin) {
                    button.disabled = false;
                    button.classList.remove('opacity-50', 'cursor-not-allowed');
                } else {
                    button.disabled = true;
                    button.classList.add('opacity-50', 'cursor-not-allowed');
                }
            });
        }

        // Event listeners setup
        function setupEventListeners() {
            // Team selector
            document.getElementById('teamSelector').addEventListener('change', function() {
                const selectedTeam = this.value;
                if (selectedTeam) {
                    showTeamSection(selectedTeam);
                } else {
                    showDashboard();
                }
            });
            
            // Login button
            document.getElementById('loginBtn').addEventListener('click', function() {
                if (currentUser.loggedIn) {
                    logout();
                } else {
                    showLoginModal();
                }
            });

            // Login form
            document.getElementById('loginForm').addEventListener('submit', function(e) {
                e.preventDefault();
                authenticateUser();
            });
            
            // Navigation tabs
            document.querySelectorAll('[data-tab]').forEach(tab => {
                tab.addEventListener('click', function() {
                    switchTab(this.dataset.tab);
                });
            });
            
            // Safety navigation
            document.querySelectorAll('[data-safety-tab]').forEach(tab => {
                tab.addEventListener('click', function() {
                    switchSafetyTab(this.dataset.safetyTab);
                });
            });
            
            // Form submissions
            document.getElementById('maintenanceForm').addEventListener('submit', function(e) {
                e.preventDefault();
                handleFormSubmission(this, currentTeam);
            });
            
            document.querySelector('#equipmentForm form').addEventListener('submit', function(e) {
                e.preventDefault();
                handleFormSubmission(this, 'equipment');
            });
            
            document.querySelector('#incidentForm form').addEventListener('submit', function(e) {
                e.preventDefault();
                handleFormSubmission(this, 'incidents');
            });
            
            document.getElementById('additionalTaskForm').addEventListener('submit', function(e) {
                e.preventDefault();
                handleFormSubmission(this, 'additionalTasks');
            });
            
            // Report buttons
            document.getElementById('generateReport').addEventListener('click', generateReport);
            document.getElementById('exportReport').addEventListener('click', exportReport);
        }

        // Initialize application when DOM is loaded
        document.addEventListener('DOMContentLoaded', initializeApp);

        // Auto-update statistics every 30 seconds
        setInterval(() => {
            updateStatistics();
            updateChartsWithRealData();
        }, 30000);
    </script>
</body>
</html>
