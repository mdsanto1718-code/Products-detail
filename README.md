<!DOCTYPE html>
<html lang="bn">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>জব নোটপ্যাড</title>
    <!-- সুন্দর ডিজাইনের জন্য Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-gray-100 p-4 font-sans">

    <div class="max-w-xl mx-auto bg-white p-6 rounded-xl shadow-md">
        <h2 class="text-2xl font-bold mb-5 text-center text-blue-600">নতুন জব নোট তৈরি করুন</h2>
        
        <form id="jobForm" class="space-y-4">
            <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                <div>
                    <label class="block text-sm font-semibold mb-1">তারিখ</label>
                    <input type="date" id="date" required class="w-full p-2 border rounded-md focus:outline-blue-500">
                </div>
                <div>
                    <label class="block text-sm font-semibold mb-1">জব নম্বর</label>
                    <input type="text" id="jobNo" placeholder="যেমন: JOB-101" required class="w-full p-2 border rounded-md focus:outline-blue-500">
                </div>
            </div>

            <div>
                <label class="block text-sm font-semibold mb-1">বিবরণ</label>
                <textarea id="description" placeholder="কাজের বিবরণ লিখুন..." class="w-full p-2 border rounded-md focus:outline-blue-500" rows="2"></textarea>
            </div>

            <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
                <div>
                    <label class="block text-sm font-semibold mb-1">কোয়ান্টিটি</label>
                    <input type="number" id="quantity" value="1" min="1" class="w-full p-2 border rounded-md focus:outline-blue-500" oninput="calculateTotal()">
                </div>
                <div>
                    <label class="block text-sm font-semibold mb-1">মূল্য (টাকা)</label>
                    <input type="number" id="price" value="0" min="0" class="w-full p-2 border rounded-md focus:outline-blue-500" oninput="calculateTotal()">
                </div>
                <div>
                    <label class="block text-sm font-semibold mb-1">মোট মূল্য</label>
                    <input type="number" id="total" value="0" readonly class="w-full p-2 border rounded-md bg-gray-100 font-bold text-green-600">
                </div>
            </div>

            <button type="submit" class="w-full bg-blue-600 text-white py-2 rounded-md font-bold hover:bg-blue-700 transition">সেভ করুন</button>
        </form>

        <hr class="my-6">

        <h3 class="text-xl font-bold mb-3 text-gray-800">সেভ করা নোটের তালিকা</h3>
        <div id="notesList" class="space-y-3"></div>
    </div>

    <script>
        // অটোমেটিক মোট মূল্য হিসাব করার ফাংশন
        function calculateTotal() {
            const qty = parseFloat(document.getElementById('quantity').value) || 0;
            const price = parseFloat(document.getElementById('price').value) || 0;
            document.getElementById('total').value = qty * price;
        }

        // ডাটা সেভ করা (LocalStorage এ থাকবে)
        document.getElementById('jobForm').addEventListener('submit', function(e) {
            e.preventDefault();
            
            const note = {
                id: Date.now(),
                date: document.getElementById('date').value,
                jobNo: document.getElementById('jobNo').value,
                description: document.getElementById('description').value,
                quantity: document.getElementById('quantity').value,
                price: document.getElementById('price').value,
                total: document.getElementById('total').value
            };

            let notes = JSON.parse(localStorage.getItem('jobNotes')) || [];
            notes.unshift(note);
            localStorage.setItem('jobNotes', JSON.stringify(notes));

            this.reset();
            document.getElementById('date').valueAsDate = new Date();
            calculateTotal();
            displayNotes();
        });

        // নোট ডিলিট করা
        function deleteNote(id) {
            let notes = JSON.parse(localStorage.getItem('jobNotes')) || [];
            notes = notes.filter(n => n.id !== id);
            localStorage.setItem('jobNotes', JSON.stringify(notes));
            displayNotes();
        }

        // সেভ করা নোট দেখানো
        function displayNotes() {
            const list = document.getElementById('notesList');
            let notes = JSON.parse(localStorage.getItem('jobNotes')) || [];
            
            if (notes.length === 0) {
                list.innerHTML = '<p class="text-gray-500 text-center text-sm">কোনো নোট সেভ করা নেই।</p>';
                return;
            }

            list.innerHTML = notes.map(n => `
                <div class="p-4 border rounded-lg bg-gray-50 shadow-sm relative">
                    <div class="flex justify-between items-center mb-2">
                        <span class="font-bold text-blue-600">জব #: ${n.jobNo}</span>
                        <span class="text-xs text-gray-500">${n.date}</span>
                    </div>
                    <p class="text-gray-700 text-sm mb-2"><strong>বিবরণ:</strong> ${n.description || 'নেই'}</p>
                    <div class="text-xs text-gray-700 grid grid-cols-3 bg-white p-2 rounded border gap-1">
                        <div><strong>পরিমাণ:</strong> ${n.quantity}</div>
                        <div><strong>একক মূল্য:</strong> ৳${n.price}</div>
                        <div class="text-green-600 font-bold"><strong>মোট:</strong> ৳${n.total}</div>
                    </div>
                    <button onclick="deleteNote(${n.id})" class="mt-2 text-xs text-red-600 hover:underline">ডিলিট করুন</button>
                </div>
            `).join('');
        }

        // শুরুতে আজকের তারিখ সেট করা
        document.getElementById('date').valueAsDate = new Date();
        displayNotes();
    </script>
</body>
</html>

