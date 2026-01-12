<template>
	<div class="feedbacks-page container">
		<h1 class="title">Fikr-mulohazalar</h1>
        <hr>

		<div class="feedback-list">
            <h4></h4>
			<div v-for="item in feedbacks" :key="item.id" class="feedback-card">
				<div class="left">
					<div class="avatar" :title="item.clientName || item.filial">{{ getInitials(item) }}</div>
					<div class="rating">
						<template v-for="n in 5" :key="n">
							<i :class="['star', n <= item.rating ? 'filled' : '']">&#9733;</i>
						</template>
						<div class="date">{{ formatDate(item.date) }}</div>
					</div>
				</div>

				<div class="content">
					<div class="header">
						<div class="recipient">Qabul qiluvchi: <strong>{{ item.recipientCode }}</strong></div>
						<div class="client">Mijoz: <strong>{{ item.clientName }}</strong></div>
						<div class="filial">{{ item.filial }}</div>
					</div>
					<p class="text">{{ item.text }}</p>
				</div>
			</div>
			<div v-if="feedbacks.length === 0" class="empty">Hozircha fikr-mulohaza yo'q.</div>
		</div>
	</div>
</template>

<script setup>
import { ref } from 'vue';

const feedbacks = ref([
	{
		id: 1,
		rating: 5,
		clientName: 'Ali Ahmedov',
		text: "Xodim juda yordamchi va professional, muammo tezkor hal qilindi.",
		recipientCode: 'EMP-001',
		filial: 'Toshkent',
		date: '2026-01-08T10:24:00Z'
	},
	{
		id: 2,
		rating: 4,
		clientName: 'Nilufar Saidova',
		text: 'Yaxshi xizmat, biroz kechikish bor edi.',
		recipientCode: 'EMP-045',
		filial: 'Samarqand',
		date: '2026-01-05T14:10:00Z'
	},
	{
		id: 3,
		rating: 3,
		clientName: 'Bekzod Iskandarov',
		text: 'Odatdagi xizmat, takliflarimni eshitishdi.',
		recipientCode: 'EMP-023',
		filial: 'Farg‘ona',
		date: '2025-12-28T08:00:00Z'
	}
]);

function formatDate(iso) {
	try {
		const d = new Date(iso);
		return d.toLocaleString();
	} catch (e) {
		return iso;
	}
}

function getInitials(item) {
	const source = item.clientName || item.filial || item.recipientCode || '';
	const parts = source.split(/\s+/).filter(Boolean);
	if (parts.length === 0) return 'UK';
	if (parts.length === 1) return parts[0].slice(0,2).toUpperCase();
	return (parts[0][0] + parts[1][0]).toUpperCase();
}
</script>

<style scoped>
:root {
	--bg: #f7f8fb;
	--card: #ffffff;
	--muted: #6b7280;
	--accent: linear-gradient(135deg,#ffd89b 0%,#19547b 100%);
}
.feedbacks-page { padding: 20px; background: var(--bg); min-height: 100vh; }
.title { font-size: 22px; margin-bottom: 16px; color: #111827; }
.feedback-list { display: flex; flex-direction: column; gap: 14px; }
.feedback-card {
	display: flex; gap: 14px; align-items: flex-start; background: var(--card);
	padding: 14px; border-radius: 12px; box-shadow: 0 6px 18px rgba(16,24,40,0.06);
	transition: transform .18s ease, box-shadow .18s ease;
}
.feedback-card:hover { transform: translateY(-4px); box-shadow: 0 10px 30px rgba(16,24,40,0.08); }
.left { display:flex; gap:12px; align-items:flex-start; width:120px; }
.avatar {
	width:48px; height:48px; border-radius:10px; display:flex; align-items:center; justify-content:center;
	background: linear-gradient(135deg,#667eea,#764ba2); color:#fff; font-weight:700; font-size:14px;
}
.rating { display:flex; flex-direction:column; align-items:flex-start; }
.stars { display:flex; gap:4px; }
.star { font-size:16px; color:#e6e6e6; }
.star.filled { color:#f5a623; text-shadow: 0 1px 0 rgba(0,0,0,0.04); }
.date { font-size:12px; color:var(--muted); margin-top:8px; }
.content { flex:1; }
.header { display:flex; justify-content:space-between; gap:12px; align-items:center; margin-bottom:8px; }
.recipient { font-size:13px; color:#111827; }
.filial { font-size:12px; color:var(--muted); background:#f3f4f6; padding:6px 8px; border-radius:8px; }
.text { margin:0; color:#374151; line-height:1.45; display:-webkit-box; -webkit-line-clamp:3; -webkit-box-orient:vertical; overflow:hidden; }
.empty { text-align:center; color:var(--muted); padding:24px; }

@media (max-width: 640px) {
	.feedback-list { gap: 12px; }
	.feedback-card { flex-direction: column; gap: 10px; padding:12px; }
	.left { width:100%; display:flex; align-items:center; gap:12px; }
	.avatar { width:42px; height:42px; border-radius:50%; font-size:13px; }
	.rating { flex-direction:row; align-items:center; gap:8px; }
	.stars { order: 0; }
	.date { font-size:11px; color:var(--muted); margin-top:0; }
	.header { flex-direction:column; align-items:flex-start; gap:6px; }
	.filial { padding:4px 6px; font-size:11px; }
	.text { font-size:14px; -webkit-line-clamp:4; }
}

@media (max-width: 420px) {
	.feedback-card { padding:10px; }
	.avatar { width:38px; height:38px; }
	.recipient { font-size:13px; }
	.date { font-size:10px; }
}
</style>

