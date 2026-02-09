<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    
    <!-- Configuración PWA para Vercel -->
    <meta name="mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <meta name="theme-color" content="#020617">
    
    <title>Billetera Emprendedor Pro</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;800&display=swap');
        
        body { 
            font-family: 'Plus Jakarta Sans', sans-serif; 
            background-color: #020617;
            color: #f8fafc;
            touch-action: manipulation;
        }

        .glass { 
            background: rgba(15, 23, 42, 0.8);
            backdrop-filter: blur(10px);
            border: 1px solid rgba(51, 65, 85, 0.5);
        }

        .active-scale:active { transform: scale(0.96); transition: 0.1s; }
        
        .no-scrollbar::-webkit-scrollbar { display: none; }

        .modal-overlay {
            display: none;
            position: fixed;
            inset: 0;
            background: rgba(0,0,0,0.8);
            z-index: 100;
            backdrop-filter: blur(4px);
        }
        .modal-overlay.active { display: flex; }

        /* Ajustes para iPhone y Android con Notch */
        .safe-top { padding-top: env(safe-area-inset-top); }
        .safe-bottom { padding-bottom: env(safe-area-inset-bottom); }
    </style>
</head>
<body class="pb-20">

    <!-- Header -->
    <nav class="sticky top-0 z-50 glass border-b border-slate-800 px-5 py-4 safe-top flex justify-between items-center">
        <div>
            <p class="text-[10px] font-bold text-indigo-400 uppercase tracking-widest">Balance Total</p>
            <h1 id="main-balance" class="text-3xl font-extrabold text-white">$ 0</h1>
        </div>
        <div class="flex gap-2">
            <button onclick="openModal('inventory-modal')" class="bg-indigo-600 px-4 py-2 rounded-xl text-xs font-bold shadow-lg shadow-indigo-900 active-scale">MENU</button>
        </div>
    </nav>

    <main class="p-4 space-y-6">
        
        <!-- Grid de Productos -->
        <section>
            <h2 class="text-xs font-bold text-slate-500 uppercase mb-3 ml-1">Venta Rápida</h2>
            <div id="pos-grid" class="grid grid-cols-2 gap-3">
                <!-- Inyectado por JS -->
            </div>
        </section>

        <!-- Panel de Cobro -->
        <section class="glass rounded-[2rem] p-6 shadow-2xl">
            <div class="flex justify-between items-end mb-4">
                <h2 class="font-bold text-xl">Ticket</h2>
                <button onclick="clearCart()" class="text-[10px] text-slate-500 font-bold border border-slate-800 px-2 py-1 rounded-lg">LIMPIAR</button>
            </div>
            
            <div id="cart-items" class="space-y-3 mb-6 max-h-48 overflow-y-auto no-scrollbar">
                <!-- Items del carrito -->
            </div>

            <div class="border-t border-slate-800 pt-5 space-y-4">
                <div class="flex justify-between items-center">
                    <span class="text-slate-400 font-medium text-lg">Subtotal:</span>
                    <span id="cart-total" class="text-2xl font-black text-indigo-400">$ 0</span>
                </div>
                
                <div class="grid grid-cols-2 gap-3">
                    <div class="space-y-1">
                        <label class="text-[10px] font-bold text-slate-500 uppercase ml-1">Paga con:</label>
                        <input type="number" id="pay-amount" inputmode="decimal" class="w-full bg-slate-900 border border-slate-700 p-4 rounded-2xl text-xl font-bold outline-none focus:border-indigo-500" placeholder="0">
                    </div>
                    <div class="space-y-1">
                        <label class="text-[10px] font-bold text-slate-500 uppercase ml-1">Vuelto:</label>
                        <div class="w-full bg-emerald-950/20 border border-emerald-500/30 p-4 rounded-2xl text-xl font-bold text-emerald-400">
                            <span id="change-amount">$ 0</span>
                        </div>
                    </div>
                </div>

                <button onclick="confirmSale()" class="w-full py-5 bg-indigo-600 rounded-2xl font-extrabold text-white active-scale shadow-xl shadow-indigo-950/50 text-lg">
                    REGISTRAR VENTA
                </button>
            </div>
        </section>

        <!-- Historial Reciente -->
        <section>
            <h2 class="text-xs font-bold text-slate-500 uppercase mb-3 ml-1">Últimos Movimientos</h2>
            <div id="history-log" class="space-y-2">
                <!-- Historial -->
            </div>
        </section>

    </main>

    <!-- Modal Gestión -->
    <div id="inventory-modal" class="modal-overlay items-end justify-center">
        <div class="bg-slate-900 w-full max-w-md rounded-t-[2.5rem] flex flex-col max-h-[85vh] border-t border-slate-800 animate-in slide-in-from-bottom duration-300">
            <div class="p-6 border-b border-slate-800 flex justify-between items-center">
                <h3 class="font-bold text-xl">Configuración</h3>
                <button onclick="closeModal('inventory-modal')" class="w-10 h-10 flex items-center justify-center bg-slate-800 rounded-full">✕</button>
            </div>
            
            <div class="p-6 overflow-y-auto no-scrollbar space-y-8 pb-20">
                <!-- Agregar Producto -->
                <div class="space-y-4">
                    <h4 class="text-xs font-bold text-indigo-400 uppercase">Nuevo Producto</h4>
                    <div class="flex gap-2">
                        <input type="text" id="new-name" placeholder="Nombre" class="flex-1 bg-slate-800 p-4 rounded-xl outline-none border border-slate-700">
                        <input type="number" id="new-price" placeholder="$" class="w-24 bg-slate-800 p-4 rounded-xl outline-none border border-slate-700 font-bold">
                    </div>
                    <button onclick="addProduct()" class="w-full py-4 bg-slate-100 text-slate-900 rounded-xl font-bold active-scale">GUARDAR PRODUCTO</button>
                </div>

                <!-- Lista de Productos -->
                <div class="space-y-4">
                    <h4 class="text-xs font-bold text-slate-500 uppercase">Mis Productos</h4>
                    <div id="full-inventory" class="grid gap-2"></div>
                </div>

                <!-- Reset -->
                <button onclick="clearAllData()" class="w-full py-4 border border-rose-500/30 text-rose-500 rounded-xl font-bold text-xs uppercase tracking-widest">Borrar todo el historial</button>
            </div>
        </div>
    </div>

    <script>
        let state = {
            balance: 0,
            products: [
                { id: 1, name: 'Café', price: 1200 },
                { id: 2, name: 'Medialuna', price: 800 },
                { id: 3, name: 'Tostado', price: 3500 }
            ],
            cart: [],
            history: []
        };

        function save() { localStorage.setItem('biz_pro_v5', JSON.stringify(state)); }
        
        function load() {
            const data = localStorage.getItem('biz_pro_v5');
            if(data) state = JSON.parse(data);
            render();
        }

        function render() {
            // Balance
            document.getElementById('main-balance').innerText = `$ ${state.balance.toLocaleString()}`;

            // Botones de venta
            const grid = document.getElementById('pos-grid');
            grid.innerHTML = state.products.map(p => `
                <button onclick="addToCart(${p.id})" class="glass p-4 rounded-2xl flex flex-col items-start active-scale shadow-sm">
                    <span class="text-[10px] font-bold text-slate-500 uppercase truncate w-full mb-1">${p.name}</span>
                    <span class="text-lg font-black text-white">$ ${p.price.toLocaleString()}</span>
                </button>
            `).join('');

            // Ticket / Carrito
            const cartEl = document.getElementById('cart-items');
            const total = state.cart.reduce((s, i) => s + (i.price * i.qty), 0);
            
            if(state.cart.length === 0) {
                cartEl.innerHTML = `<p class="text-center text-slate-600 text-sm py-4">Sin productos en el ticket</p>`;
            } else {
                cartEl.innerHTML = state.cart.map(item => `
                    <div class="flex justify-between items-center bg-slate-800/30 p-3 rounded-xl">
                        <div class="flex items-center gap-3">
                            <span class="bg-indigo-600 text-[10px] font-bold px-2 py-1 rounded-md">${item.qty}</span>
                            <span class="text-sm font-medium">${item.name}</span>
                        </div>
                        <span class="font-bold text-indigo-400">$ ${(item.qty * item.price).toLocaleString()}</span>
                    </div>
                `).join('');
            }

            document.getElementById('cart-total').innerText = `$ ${total.toLocaleString()}`;
            
            // Cálculo de vuelto
            const pay = parseFloat(document.getElementById('pay-amount').value) || 0;
            const change = total > 0 ? Math.max(0, pay - total) : 0;
            document.getElementById('change-amount').innerText = `$ ${change.toLocaleString()}`;

            // Historial
            const histEl = document.getElementById('history-log');
            histEl.innerHTML = state.history.slice(-5).reverse().map(h => `
                <div class="glass p-3 rounded-xl flex justify-between items-center border-l-4 border-l-emerald-500 mb-2">
                    <div>
                        <p class="text-[9px] font-bold text-slate-500">${h.date}</p>
                        <p class="text-xs font-bold text-slate-300 truncate w-40">${h.desc}</p>
                    </div>
                    <span class="font-black text-white">$ ${h.amount.toLocaleString()}</span>
                </div>
            `).join('');

            // Lista en Modal
            const invEl = document.getElementById('full-inventory');
            invEl.innerHTML = state.products.map(p => `
                <div class="flex justify-between items-center p-4 bg-slate-800/50 rounded-xl border border-slate-700/50">
                    <span class="font-bold text-sm">${p.name} ($${p.price})</span>
                    <button onclick="removeProduct(${p.id})" class="text-rose-500 text-[10px] font-black">BORRAR</button>
                </div>
            `).join('');
        }

        function addToCart(id) {
            const p = state.products.find(x => x.id === id);
            const inCart = state.cart.find(x => x.id === id);
            if(inCart) inCart.qty++;
            else state.cart.push({...p, qty: 1});
            render();
            if(navigator.vibrate) navigator.vibrate(10);
        }

        function clearCart() { state.cart = []; render(); }

        function confirmSale() {
            const total = state.cart.reduce((s, i) => s + (i.price * i.qty), 0);
            if(total === 0) return;
            
            state.balance += total;
            state.history.push({
                date: new Date().toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'}),
                type: 'Venta',
                amount: total,
                desc: state.cart.map(c => `${c.qty} ${c.name}`).join(', ')
            });
            
            clearCart();
            document.getElementById('pay-amount').value = '';
            save();
            render();
            if(navigator.vibrate) navigator.vibrate([30, 50, 30]);
        }

        function addProduct() {
            const name = document.getElementById('new-name').value;
            const price = parseInt(document.getElementById('new-price').value);
            if(!name || !price) return;
            state.products.push({id: Date.now(), name, price});
            document.getElementById('new-name').value = '';
            document.getElementById('new-price').value = '';
            save();
            render();
        }

        function removeProduct(id) {
            state.products = state.products.filter(p => p.id !== id);
            save();
            render();
        }

        function clearAllData() {
            if(confirm('¿Estás seguro de borrar todo?')) {
                state.balance = 0;
                state.history = [];
                save();
                render();
            }
        }

        function openModal(id) { document.getElementById(id).classList.add('active'); }
        function closeModal(id) { document.getElementById(id).classList.remove('active'); }

        document.getElementById('pay-amount').addEventListener('input', render);
        window.onload = load;
    </script>
</body>
</html>
