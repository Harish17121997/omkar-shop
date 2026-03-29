<template>
  <div class="layout">

    <!-- ══ SIDEBAR ══ -->
    <aside class="sidebar">
      <div class="sb-brand">
        <img src="../public/favicon.avif" class="sb-brand-icon" alt="logo">
        <div>
          <div class="sb-brand-name">Shree Renuka</div>
          <div class="sb-brand-sub">SBT Management</div>
        </div>
      </div>
      <nav class="sb-nav">
        <div class="sb-item" :class="{ 'sb-active': activeTab === 'dashboard' }" @click="activeTab = 'dashboard'">
          <svg width="15" height="15" viewBox="0 0 15 15" fill="none"><rect x="1" y="1" width="5.5" height="5.5" rx="1" fill="currentColor"/><rect x="8.5" y="1" width="5.5" height="5.5" rx="1" fill="currentColor"/><rect x="1" y="8.5" width="5.5" height="5.5" rx="1" fill="currentColor"/><rect x="8.5" y="8.5" width="5.5" height="5.5" rx="1" fill="currentColor"/></svg>
          Dashboard
        </div>
        <div class="sb-item" :class="{ 'sb-active': activeTab === 'customers' }" @click="activeTab = 'customers'">
          <svg width="15" height="15" viewBox="0 0 15 15" fill="none"><circle cx="7.5" cy="5" r="3" stroke="currentColor" stroke-width="1.3"/><path d="M2 13c0-3 2.5-5 5.5-5s5.5 2 5.5 5" stroke="currentColor" stroke-width="1.3" stroke-linecap="round"/></svg>
          Customers
        </div>
        <div class="sb-item" :class="{ 'sb-active': activeTab === 'payments' }" @click="activeTab = 'payments'">
          <svg width="15" height="15" viewBox="0 0 15 15" fill="none"><rect x="1.5" y="3.5" width="12" height="8.5" rx="1.5" stroke="currentColor" stroke-width="1.3"/><path d="M1.5 6.5h12" stroke="currentColor" stroke-width="1.3"/></svg>
          Payments
        </div>
        <div class="sb-item" :class="{ 'sb-active': activeTab === 'reports' }" @click="activeTab = 'reports'">
          <svg width="15" height="15" viewBox="0 0 15 15" fill="none"><path d="M2 12V4l5-2.5L12 4v8l-5 2.5L2 12Z" stroke="currentColor" stroke-width="1.3" stroke-linejoin="round"/></svg>
          Reports
        </div>
      </nav>
      <div class="sb-bottom">
        <div class="sb-section-label">Summary</div>
        <div class="sb-stat-card">
          <div class="sb-st-row"><span class="sb-st-label">Total Bills</span><span class="sb-st-val" style="color:#F87171">₹{{ fmt(allDebit) }}</span></div>
          <div class="sb-st-row"><span class="sb-st-label">Collected</span><span class="sb-st-val" style="color:#4ADE80">₹{{ fmt(allCredit) }}</span></div>
          <div class="sb-divider"></div>
          <div class="sb-st-row"><span class="sb-st-label">Pending</span><span class="sb-st-val" style="color:#FBBF24">₹{{ fmt(allBalance) }}</span></div>
        </div>
      </div>
    </aside>

    <!-- ══ MAIN ══ -->
    <div class="main">

      <!-- ─── DASHBOARD TAB ─────────────────────────────── -->
      <template v-if="activeTab === 'dashboard'">
        <div class="topbar">
          <div><div class="topbar-title">Dashboard</div><div class="topbar-sub">Shree Renuka Ladger</div></div>
          <button class="btn-add" @click="openAddModal">
            <svg width="13" height="13" viewBox="0 0 13 13" fill="none"><path d="M6.5 1v11M1 6.5h11" stroke="currentColor" stroke-width="2" stroke-linecap="round"/></svg>
            Add Customer
          </button>
        </div>
        <div class="chips">
          <div class="chip">
            <div class="chip-top"><div><div class="chip-num" style="color:#2563EB">{{ uniqueCustomers.length }}</div><div class="chip-lbl">Total Customers</div></div><div class="chip-icon" style="background:#EFF6FF">👥</div></div>
            <div class="chip-note" style="color:#2563EB">{{ allPending }} accounts pending</div>
          </div>
          <div class="chip">
            <div class="chip-top"><div><div class="chip-num" style="color:#DC2626">₹{{ fmt(allDebit) }}</div><div class="chip-lbl">Total Billed</div></div><div class="chip-icon" style="background:#FEF2F2">📤</div></div>
            <div class="chip-note" style="color:#DC2626">Total debit</div>
          </div>
          <div class="chip">
            <div class="chip-top"><div><div class="chip-num" style="color:#16A34A">₹{{ fmt(allCredit) }}</div><div class="chip-lbl">Collected</div></div><div class="chip-icon" style="background:#F0FDF4">✅</div></div>
            <div class="chip-note" style="color:#16A34A">Amount received</div>
          </div>
          <div class="chip">
            <div class="chip-top"><div><div class="chip-num" style="color:#D97706">₹{{ fmt(allBalance) }}</div><div class="chip-lbl">Outstanding</div></div><div class="chip-icon" style="background:#FFFBEB">⏳</div></div>
            <div class="chip-note" style="color:#D97706">{{ allPending }} accounts due</div>
          </div>
        </div>
        <div class="tbl-card" style="margin-top:16px">
          <div class="tbl-header">
            <span class="tbl-title">Recent Customers</span>
            <button class="link-btn" @click="activeTab = 'customers'">View All →</button>
          </div>
          <div class="tbl-wrap">
            <table class="tbl">
              <thead><tr><th>#</th><th>Customer</th><th>Mobile</th><th>Transactions</th><th class="ra">Balance ₹</th><th>Status</th></tr></thead>
              <tbody>
                <tr v-for="(c, i) in uniqueCustomers.slice(0, 8)" :key="c.mobile || c.name" @click="openViewModal(c)" style="cursor:pointer">
                  <td class="td-sl">{{ i + 1 }}</td>
                  <td><div class="name-cell"><div class="av" :style="{ background: avColor(c.name) }">{{ initials(c.name) }}</div><span class="name-txt">{{ c.name }}</span></div></td>
                  <td class="td-dim">{{ c.mobile || '—' }}</td>
                  <td><span class="txn-badge">{{ c.transactions.length }} txn{{ c.transactions.length !== 1 ? 's' : '' }}</span></td>
                  <td class="ra"><span class="bal-pill" :class="c.totalBalance > 0 ? 'bp-red' : 'bp-grn'">{{ fmt(c.totalBalance) }}</span></td>
                  <td><span class="status-badge" :class="c.totalBalance <= 0 ? 'sb-ok' : 'sb-due'">{{ c.totalBalance <= 0 ? 'Cleared' : 'Pending' }}</span></td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </template>

      <!-- ─── CUSTOMERS TAB ─────────────────────────────── -->
      <template v-if="activeTab === 'customers'">
        <div class="topbar">
          <div><div class="topbar-title">Customers</div><div class="topbar-sub">All customer records</div></div>
          <button class="btn-add" @click="openAddModal">
            <svg width="13" height="13" viewBox="0 0 13 13" fill="none"><path d="M6.5 1v11M1 6.5h11" stroke="currentColor" stroke-width="2" stroke-linecap="round"/></svg>
            Add Customer
          </button>
        </div>
        <div class="filter-bar">
          <div class="search-box">
            <svg class="search-ico" width="14" height="14" viewBox="0 0 14 14" fill="none"><circle cx="6" cy="6" r="4.5" stroke="currentColor" stroke-width="1.5"/><path d="M10 10l3 3" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/></svg>
            <input class="search-inp" type="text" placeholder="Search by name or mobile…" v-model="filterName" />
          </div>
          <select class="f-select" v-model="filterBalance">
            <option value="">All Status</option>
            <option value="pending">Pending</option>
            <option value="cleared">Cleared</option>
          </select>
          <button class="btn-clear" @click="clearFilters">✕ Clear</button>
        </div>

        <div v-if="isLoading" class="state-center"><div class="loader-ring"></div><p>Loading…</p></div>
        <div v-else-if="filteredUniqueCustomers.length === 0" class="state-center">
          <div style="font-size:40px">📋</div>
          <p style="font-size:16px;font-weight:600;color:#374151">No records found</p>
          <p style="font-size:13px;color:#9CA3AF">Try adjusting filters</p>
        </div>
        <div v-else class="tbl-card">
          <div class="tbl-header">
            <span class="tbl-title">All Records</span>
            <span class="tbl-badge">{{ filteredUniqueCustomers.length }} customers</span>
          </div>
          <div class="tbl-wrap">
            <table class="tbl">
              <thead><tr>
                <th style="width:40px">#</th><th>Customer</th><th>Mobile</th>
                <th>Transactions</th><th class="ra">Total Billed ₹</th>
                <th class="ra">Total Paid ₹</th><th class="ra">Balance ₹</th><th>Status</th><th>Actions</th>
              </tr></thead>
              <tbody>
                <tr v-for="(c, i) in filteredUniqueCustomers" :key="c.mobile || c.name" :class="{ 'tr-cleared': c.totalBalance <= 0 }">
                  <td class="td-sl">{{ i + 1 }}</td>
                  <td><div class="name-cell"><div class="av" :style="{ background: avColor(c.name) }">{{ initials(c.name) }}</div><div><span class="name-txt">{{ c.name }}</span><div class="td-dim" style="font-size:11px">{{ c.alternateName || '' }}</div></div></div></td>
                  <td class="td-dim">{{ c.mobile || '—' }}</td>
                  <td><span class="txn-badge">{{ c.transactions.length }} txn{{ c.transactions.length !== 1 ? 's' : '' }}</span></td>
                  <td class="ra td-red td-mono">{{ fmt(c.totalDebit) }}</td>
                  <td class="ra td-grn td-mono">{{ fmt(c.totalCredit) }}</td>
                  <td class="ra"><span class="bal-pill" :class="c.totalBalance > 0 ? 'bp-red' : 'bp-grn'">{{ fmt(c.totalBalance) }}</span></td>
                  <td><span class="status-badge" :class="c.totalBalance <= 0 ? 'sb-ok' : 'sb-due'">{{ c.totalBalance <= 0 ? 'Cleared' : 'Pending' }}</span></td>
                  <td>
                    <div class="acts">
                      <button class="ab ab-v" @click="openViewModal(c)" title="View Transactions">
                        <svg width="13" height="13" viewBox="0 0 13 13" fill="none"><circle cx="6.5" cy="6.5" r="4" stroke="currentColor" stroke-width="1.4"/><circle cx="6.5" cy="6.5" r="1.5" fill="currentColor"/></svg>
                      </button>
                      <button class="ab ab-p" @click="openPaymentModal(c)" title="Add Transaction">
                        <svg width="13" height="13" viewBox="0 0 13 13" fill="none"><rect x="1.5" y="3" width="10" height="7.5" rx="1.5" stroke="currentColor" stroke-width="1.3"/><path d="M1.5 6h10" stroke="currentColor" stroke-width="1.3"/></svg>
                      </button>
                      <button class="ab ab-d" @click="confirmDelete(c)" title="Delete All Records">
                        <svg width="13" height="13" viewBox="0 0 13 13" fill="none"><path d="M2 4h9M5 4V2.5h3V4M5.5 5.5v4M7.5 5.5v4M3 4l.5 6.5h6L10 4" stroke="currentColor" stroke-width="1.3" stroke-linecap="round" stroke-linejoin="round"/></svg>
                      </button>
                      <button v-if="c.totalBalance > 0" class="ab ab-wa" @click="sendWhatsAppReminder(c)" title="Send WhatsApp Reminder">
                          <svg width="13" height="13" viewBox="0 0 24 24" fill="currentColor">
                            <path d="M17.472 14.382c-.297-.149-1.758-.867-2.03-.967-.273-.099-.471-.148-.67.15-.197.297-.767.966-.94 1.164-.173.199-.347.223-.644.075-.297-.15-1.255-.463-2.39-1.475-.883-.788-1.48-1.761-1.653-2.059-.173-.297-.018-.458.13-.606.134-.133.298-.347.446-.52.149-.174.198-.298.298-.497.099-.198.05-.371-.025-.52-.075-.149-.669-1.612-.916-2.207-.242-.579-.487-.5-.669-.51-.173-.008-.371-.01-.57-.01-.198 0-.52.074-.792.372-.272.297-1.04 1.016-1.04 2.479 0 1.462 1.065 2.875 1.213 3.074.149.198 2.096 3.2 5.077 4.487.709.306 1.262.489 1.694.625.712.227 1.36.195 1.871.118.571-.085 1.758-.719 2.006-1.413.248-.694.248-1.289.173-1.413-.074-.124-.272-.198-.57-.347z"/>
                            <path d="M12 0C5.373 0 0 5.373 0 12c0 2.117.549 4.1 1.51 5.83L.057 23.273a.5.5 0 0 0 .67.593l5.596-1.469A11.944 11.944 0 0 0 12 24c6.627 0 12-5.373 12-12S18.627 0 12 0zm0 21.818a9.812 9.812 0 0 1-5.001-1.369l-.358-.214-3.32.872.888-3.24-.235-.374A9.818 9.818 0 0 1 2.182 12C2.182 6.57 6.57 2.182 12 2.182S21.818 6.57 21.818 12 17.43 21.818 12 21.818z"/>
                          </svg>
                      </button>
                    </div>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </template>

      <!-- ─── PAYMENTS TAB ──────────────────────────────── -->
      <template v-if="activeTab === 'payments'">
        <div class="topbar">
          <div><div class="topbar-title">Payments</div><div class="topbar-sub">All payment records & outstanding dues</div></div>
        </div>
        <div class="chips">
          <div class="chip"><div class="chip-top"><div><div class="chip-num" style="color:#16A34A">₹{{ fmt(allCredit) }}</div><div class="chip-lbl">Total Collected</div></div><div class="chip-icon" style="background:#F0FDF4">💰</div></div><div class="chip-note" style="color:#16A34A">Across all accounts</div></div>
          <div class="chip"><div class="chip-top"><div><div class="chip-num" style="color:#D97706">₹{{ fmt(allBalance) }}</div><div class="chip-lbl">Outstanding</div></div><div class="chip-icon" style="background:#FFFBEB">⏳</div></div><div class="chip-note" style="color:#D97706">Still to collect</div></div>
          <div class="chip"><div class="chip-top"><div><div class="chip-num" style="color:#DC2626">{{ allPending }}</div><div class="chip-lbl">Pending Accounts</div></div><div class="chip-icon" style="background:#FEF2F2">🔴</div></div><div class="chip-note" style="color:#DC2626">Need follow-up</div></div>
          <div class="chip"><div class="chip-top"><div><div class="chip-num" style="color:#16A34A">{{ uniqueCustomers.length - allPending }}</div><div class="chip-lbl">Cleared Accounts</div></div><div class="chip-icon" style="background:#F0FDF4">✅</div></div><div class="chip-note" style="color:#16A34A">Fully paid</div></div>
        </div>
        <div class="tbl-card" style="margin-top:16px">
          <div class="tbl-header">
            <span class="tbl-title">⏳ Pending Dues</span>
            <span class="tbl-badge" style="background:#FEF2F2;color:#DC2626">{{ pendingCustomers.length }} accounts</span>
          </div>
          <div v-if="pendingCustomers.length === 0" class="state-center" style="padding:40px"><p style="color:#16A34A;font-weight:600">🎉 All accounts are cleared!</p></div>
          <div v-else class="tbl-wrap">
            <table class="tbl">
              <thead><tr><th>#</th><th>Customer</th><th>Mobile</th><th>Txns</th><th class="ra">Total Bill ₹</th><th class="ra">Paid ₹</th><th class="ra">Due ₹</th><th>Action</th><th>WhatsApp</th></tr></thead>
              <tbody>
                <tr v-for="(c, i) in pendingCustomers" :key="c.mobile || c.name">
                  <td class="td-sl">{{ i + 1 }}</td>
                  <td><div class="name-cell"><div class="av" :style="{ background: avColor(c.name) }">{{ initials(c.name) }}</div><span class="name-txt">{{ c.name }}</span></div></td>
                  <td class="td-dim">{{ c.mobile || '—' }}</td>
                  <td><span class="txn-badge">{{ c.transactions.length }}</span></td>
                  <td class="ra td-mono td-red">{{ fmt(c.totalDebit) }}</td>
                  <td class="ra td-mono td-grn">{{ fmt(c.totalCredit) }}</td>
                  <td class="ra"><span class="bal-pill bp-red">{{ fmt(c.totalBalance) }}</span></td>
                  <td><button class="btn-pay-now" @click="openPaymentModal(c)">+ Transaction</button></td>
                  <td><button class="ab ab-wa" @click="sendWhatsAppReminder(c)" title="Send Reminder">
                      <svg width="13" height="13" viewBox="0 0 24 24" fill="currentColor">
                        <path d="M17.472 14.382c-.297-.149-1.758-.867-2.03-.967-.273-.099-.471-.148-.67.15-.197.297-.767.966-.94 1.164-.173.199-.347.223-.644.075-.297-.15-1.255-.463-2.39-1.475-.883-.788-1.48-1.761-1.653-2.059-.173-.297-.018-.458.13-.606.134-.133.298-.347.446-.52.149-.174.198-.298.298-.497.099-.198.05-.371-.025-.52-.075-.149-.669-1.612-.916-2.207-.242-.579-.487-.5-.669-.51-.173-.008-.371-.01-.57-.01-.198 0-.52.074-.792.372-.272.297-1.04 1.016-1.04 2.479 0 1.462 1.065 2.875 1.213 3.074.149.198 2.096 3.2 5.077 4.487.709.306 1.262.489 1.694.625.712.227 1.36.195 1.871.118.571-.085 1.758-.719 2.006-1.413.248-.694.248-1.289.173-1.413-.074-.124-.272-.198-.57-.347z"/>
                        <path d="M12 0C5.373 0 0 5.373 0 12c0 2.117.549 4.1 1.51 5.83L.057 23.273a.5.5 0 0 0 .67.593l5.596-1.469A11.944 11.944 0 0 0 12 24c6.627 0 12-5.373 12-12S18.627 0 12 0zm0 21.818a9.812 9.812 0 0 1-5.001-1.369l-.358-.214-3.32.872.888-3.24-.235-.374A9.818 9.818 0 0 1 2.182 12C2.182 6.57 6.57 2.182 12 2.182S21.818 6.57 21.818 12 17.43 21.818 12 21.818z"/>
                      </svg></button>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
        <div class="tbl-card" style="margin-top:16px">
          <div class="tbl-header">
            <span class="tbl-title">✅ Cleared Accounts</span>
            <span class="tbl-badge" style="background:#F0FDF4;color:#16A34A">{{ clearedCustomers.length }} accounts</span>
          </div>
          <div v-if="clearedCustomers.length === 0" class="state-center" style="padding:40px"><p style="color:#9CA3AF">No cleared accounts yet</p></div>
          <div v-else class="tbl-wrap">
            <table class="tbl">
              <thead><tr><th>#</th><th>Customer</th><th>Mobile</th><th class="ra">Total Bill ₹</th><th class="ra">Paid ₹</th><th>Status</th></tr></thead>
              <tbody>
                <tr v-for="(c, i) in clearedCustomers" :key="c.mobile || c.name" class="tr-cleared">
                  <td class="td-sl">{{ i + 1 }}</td>
                  <td><div class="name-cell"><div class="av" :style="{ background: avColor(c.name) }">{{ initials(c.name) }}</div><span class="name-txt">{{ c.name }}</span></div></td>
                  <td class="td-dim">{{ c.mobile || '—' }}</td>
                  <td class="ra td-mono">{{ fmt(c.totalDebit) }}</td>
                  <td class="ra td-mono td-grn">{{ fmt(c.totalCredit) }}</td>
                  <td><span class="status-badge sb-ok">Cleared</span></td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </template>

      <!-- ─── REPORTS TAB ───────────────────────────────── -->
      <template v-if="activeTab === 'reports'">
        <div class="topbar"><div><div class="topbar-title">Reports</div><div class="topbar-sub">Account summary & analytics</div></div></div>
        <div class="report-grid">
          <div class="report-card rc-blue"><div class="rc-icon">👥</div><div class="rc-val">{{ uniqueCustomers.length }}</div><div class="rc-lbl">Total Customers</div></div>
          <div class="report-card rc-red"><div class="rc-icon">📤</div><div class="rc-val">₹{{ fmt(allDebit) }}</div><div class="rc-lbl">Total Billed</div></div>
          <div class="report-card rc-green"><div class="rc-icon">📥</div><div class="rc-val">₹{{ fmt(allCredit) }}</div><div class="rc-lbl">Total Collected</div></div>
          <div class="report-card rc-amber"><div class="rc-icon">⚖️</div><div class="rc-val">₹{{ fmt(allBalance) }}</div><div class="rc-lbl">Net Outstanding</div></div>
          <div class="report-card rc-purple"><div class="rc-icon">🔴</div><div class="rc-val">{{ allPending }}</div><div class="rc-lbl">Pending Accounts</div></div>
          <div class="report-card rc-teal"><div class="rc-icon">✅</div><div class="rc-val">{{ uniqueCustomers.length - allPending }}</div><div class="rc-lbl">Cleared Accounts</div></div>
        </div>
        <div class="tbl-card" style="margin-top:16px">
          <div class="tbl-header"><span class="tbl-title">Collection Progress</span></div>
          <div style="padding:20px 20px 24px">
            <div style="display:flex;justify-content:space-between;font-size:13px;color:#6B7280;margin-bottom:8px">
              <span>Collected: <strong style="color:#16A34A">₹{{ fmt(allCredit) }}</strong></span>
              <span>Total: <strong>₹{{ fmt(allDebit) }}</strong></span>
            </div>
            <div class="progress-track"><div class="progress-fill" :style="{ width: collectionPct + '%' }"></div></div>
            <div style="text-align:center;margin-top:8px;font-size:13px;color:#6B7280"><strong style="color:#16A34A;font-size:16px">{{ collectionPct }}%</strong> collected</div>
          </div>
        </div>
        <div class="tbl-card" style="margin-top:16px">
          <div class="tbl-header"><span class="tbl-title">Top Outstanding Dues</span><span class="tbl-badge">Sorted by amount</span></div>
          <div class="tbl-wrap">
            <table class="tbl">
              <thead><tr><th>#</th><th>Customer</th><th>Mobile</th><th>Txns</th><th class="ra">Total Bill ₹</th><th class="ra">Paid ₹</th><th class="ra">Balance ₹</th></tr></thead>
              <tbody>
                <tr v-for="(c, i) in topDues" :key="c.mobile || c.name">
                  <td class="td-sl">{{ i + 1 }}</td>
                  <td><div class="name-cell"><div class="av" :style="{ background: avColor(c.name) }">{{ initials(c.name) }}</div><span class="name-txt">{{ c.name }}</span></div></td>
                  <td class="td-dim">{{ c.mobile || '—' }}</td>
                  <td><span class="txn-badge">{{ c.transactions.length }}</span></td>
                  <td class="ra td-mono td-red">{{ fmt(c.totalDebit) }}</td>
                  <td class="ra td-mono td-grn">{{ fmt(c.totalCredit) }}</td>
                  <td class="ra"><span class="bal-pill bp-red">{{ fmt(c.totalBalance) }}</span></td>
                </tr>
                <tr v-if="topDues.length === 0"><td colspan="7" style="text-align:center;padding:24px;color:#9CA3AF">No pending dues 🎉</td></tr>
              </tbody>
            </table>
          </div>
        </div>
      </template>

    </div><!-- /main -->

    <!-- ══════════════════════════════════════════════════
         ADD / NEW TRANSACTION MODAL  (smart duplicate check)
    ══════════════════════════════════════════════════ -->
    <Teleport to="body">
    <div v-if="showFormModal" class="ov" @click.self="closeFormModal">
      <div class="modal modal-lg">
        <div class="mdl-hd" :class="addMode === 'new_txn' ? 'mhd-amber' : 'mhd-blue'">
          <div class="mhd-left">
            <div class="mhd-ico">{{ addMode === 'new_txn' ? '📋' : '➕' }}</div>
            <div>
              <h2>{{ addMode === 'new_txn' ? 'New Transaction' : 'Add Customer' }}</h2>
              <p v-if="addMode === 'new_txn'" style="font-size:12px;color:#92400E;margin-top:2px">Adding to existing customer: <strong>{{ matchedCustomer?.name }}</strong></p>
            </div>
          </div>
          <button class="mdl-close" @click="closeFormModal">✕</button>
        </div>

        <!-- ── STEP 1: Search / Identity ── -->
        <div class="mdl-body">

          <!-- Search box with live duplicate detection -->
          <div v-if="addMode !== 'new_txn'" class="dup-search-wrap">
            <div class="dup-search-label">Search existing customer first</div>
            <div class="dup-search-row">
              <div class="search-box" style="flex:1">
                <svg class="search-ico" width="14" height="14" viewBox="0 0 14 14" fill="none"><circle cx="6" cy="6" r="4.5" stroke="currentColor" stroke-width="1.5"/><path d="M10 10l3 3" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/></svg>
                <input
                  ref="dupSearchRef"
                  class="search-inp"
                  type="text"
                  placeholder="Type name or mobile number…"
                  v-model="dupSearchQuery"
                  @input="onDupSearch"
                  autocomplete="off"
                />
              </div>
            </div>

            <!-- Dropdown results -->
            <div v-if="dupResults.length > 0" class="dup-dropdown">
              <div class="dup-dropdown-label">{{ dupResults.length }} existing customer{{ dupResults.length > 1 ? 's' : '' }} found — select to add transaction</div>
              <div
                v-for="r in dupResults"
                :key="r.mobile || r.name"
                class="dup-result-item"
                @click="selectExistingCustomer(r)"
              >
                <div class="av" :style="{ background: avColor(r.name) }">{{ initials(r.name) }}</div>
                <div class="dup-result-info">
                  <strong>{{ r.name }}</strong>
                  <span>{{ r.mobile || 'No mobile' }} · {{ r.transactions.length }} transaction{{ r.transactions.length !== 1 ? 's' : '' }}</span>
                </div>
                <div style="text-align:right;flex-shrink:0">
                  <div class="bal-pill" :class="r.totalBalance > 0 ? 'bp-red' : 'bp-grn'" style="font-size:12px">₹{{ fmt(r.totalBalance) }}</div>
                  <div style="font-size:10px;color:#9CA3AF;margin-top:3px">{{ r.totalBalance > 0 ? 'Pending' : 'Cleared' }}</div>
                </div>
              </div>
              <div class="dup-or-new" @click="dupSearchQuery = ''; dupResults = []; addMode = 'new'">
                <svg width="12" height="12" viewBox="0 0 13 13" fill="none"><path d="M6.5 1v11M1 6.5h11" stroke="currentColor" stroke-width="2" stroke-linecap="round"/></svg>
                Not in the list? Add as new customer
              </div>
            </div>

            <!-- No results hint -->
            <div v-if="dupSearchQuery.length >= 2 && dupResults.length === 0 && !dupSearching" class="dup-no-result">
              No existing customer found for "<strong>{{ dupSearchQuery }}</strong>" —
              <span class="dup-new-link" @click="prefillFormFromSearch">fill as new customer ↓</span>
            </div>
          </div>

          <!-- If new_txn mode: show existing transactions summary -->
          <div v-if="addMode === 'new_txn' && matchedCustomer" class="existing-txn-summary">
            <div class="ets-header">
              <span>Transaction History</span>
              <span class="txn-badge">{{ matchedCustomer.transactions.length }} txn{{ matchedCustomer.transactions.length !== 1 ? 's' : '' }}</span>
            </div>
            <div class="ets-table-wrap">
              <table class="tbl" style="font-size:12px">
                <thead><tr><th>#</th><th>Date</th><th>Description</th><th>Bill No.</th><th class="ra">Debit ₹</th><th class="ra">Credit ₹</th><th class="ra">Balance ₹</th></tr></thead>
                <tbody>
                  <tr v-for="(t, i) in matchedCustomer.transactions" :key="t.id">
                    <td class="td-sl">{{ i + 1 }}</td>
                    <td class="td-dim">{{ fmtDate(t.date) }}</td>
                    <td class="td-dim">{{ t.description || '—' }}</td>
                    <td class="td-dim">{{ t.billNo || '—' }}</td>
                    <td class="ra td-red td-mono">{{ t.debit ? fmt(t.debit) : '—' }}</td>
                    <td class="ra td-grn td-mono">{{ t.credit ? fmt(t.credit) : '—' }}</td>
                    <td class="ra"><span class="bal-pill" :class="((t.debit||0)-(t.credit||0)) > 0 ? 'bp-red' : 'bp-grn'" style="font-size:11px">{{ fmt((t.debit||0)-(t.credit||0)) }}</span></td>
                  </tr>
                  <!-- Running total row -->
                  <tr style="background:#F8FAFC;font-weight:700">
                    <td colspan="4" style="padding:10px 14px;font-size:12px;color:#374151">Running Total</td>
                    <td class="ra td-red td-mono" style="padding:10px 14px">{{ fmt(matchedCustomer.totalDebit) }}</td>
                    <td class="ra td-grn td-mono" style="padding:10px 14px">{{ fmt(matchedCustomer.totalCredit) }}</td>
                    <td class="ra" style="padding:10px 14px"><span class="bal-pill" :class="matchedCustomer.totalBalance > 0 ? 'bp-red' : 'bp-grn'">{{ fmt(matchedCustomer.totalBalance) }}</span></td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>

          <!-- ── New customer fields (only in 'new' mode) ── -->
          <div v-if="addMode === 'new'">
            <div class="fsec-title" style="margin-top:16px">Personal Details</div>
            <div class="fgrid">
              <div class="fg col2"><label>Full Name <sup class="req">*</sup></label><input class="fi" v-model="form.name" placeholder="Customer full name" @input="form.name = form.name.toUpperCase()" /></div>
              <div class="fg"><label>Alt. Name / Relation</label><input class="fi" v-model="form.alternateName" placeholder="e.g. Husain / Plumber" /></div>
              <div class="fg"><label>Mobile <sup class="req">*</sup></label><input class="fi" v-model="form.mobile" type="tel" placeholder="Primary mobile" @input="onMobileInput" /></div>
              <div class="fg col2"><label>Address</label><input class="fi" v-model="form.address" placeholder="Full address" /></div>
              <div class="fg"><label>Alt. Mobile</label><input class="fi" v-model="form.altMobile" type="tel" /></div>
            </div>
          </div>

          <!-- ── Transaction fields (both modes) ── -->
          <div v-if="addMode === 'new' || addMode === 'new_txn'">
            <div class="fsec-title" style="margin-top:20px">{{ addMode === 'new_txn' ? '➕ New Transaction Details' : 'Billing Details' }}</div>
            <div class="fgrid">
              <div class="fg"><label>Date</label><input class="fi" v-model="form.date" type="date" /></div>
              <div class="fg"><label>Bill No.</label><input class="fi" v-model="form.billNo" placeholder="Bill number" /></div>
              <div class="fg"><label>Book No.</label><input class="fi" v-model="form.bookNo" placeholder="Book number" /></div>
              <div class="fg col2"><label>Description</label><input class="fi" v-model="form.description" placeholder="Work description" /></div>
              <div class="fg"><label>Total Bill (₹) <sup class="req">*</sup></label><input class="fi fi-mono" v-model.number="form.debit" type="number" placeholder="0" /></div>
              <div class="fg"><label>Paid Amount (₹)</label><input class="fi fi-mono" v-model.number="form.credit" type="number" placeholder="0" /></div>
            </div>
            <div class="bal-preview">
              <div v-if="addMode === 'new_txn' && matchedCustomer" style="margin-bottom:10px;padding-bottom:10px;border-bottom:1px dashed #E4E7EC">
                <div class="bp-row"><span style="color:#9CA3AF">Previous Balance</span><span class="fi-mono" :style="{ color: matchedCustomer.totalBalance > 0 ? '#DC2626' : '#16A34A' }">₹{{ fmt(matchedCustomer.totalBalance) }}</span></div>
              </div>
              <div class="bp-row"><span>This Transaction Bill</span><span class="fi-mono">₹{{ fmt(form.debit || 0) }}</span></div>
              <div class="bp-row"><span>Amount Paid Now</span><span class="fi-mono" style="color:#16A34A">− ₹{{ fmt(form.credit || 0) }}</span></div>
              <div class="bp-sep"></div>
              <div class="bp-row bp-total">
                <span>New Running Balance</span>
                <span class="fi-mono" :style="{ color: newRunningBalance > 0 ? '#DC2626' : '#16A34A' }">₹{{ fmt(newRunningBalance) }}</span>
              </div>
            </div>
            <p v-if="formError" class="errmsg">⚠ {{ formError }}</p>
          </div>

        </div><!-- /mdl-body -->

        <div class="mdl-ft">
          <button class="btn-cancel" @click="closeFormModal">Cancel</button>
          <button
            v-if="addMode === 'new' || addMode === 'new_txn'"
            class="btn-ok"
            :class="addMode === 'new_txn' ? 'bok-amber' : 'bok-blue'"
            :disabled="isSubmitting"
            @click="submitForm"
          >
            <span v-if="isSubmitting" class="spn"></span>
            <span v-else>{{ addMode === 'new_txn' ? 'Add Transaction' : 'Add Customer' }}</span>
          </button>
        </div>
      </div>
    </div>
    </Teleport>

    <!-- ══ VIEW MODAL (with full transaction history) ══ -->
    <Teleport to="body">
    <div v-if="showViewModal" class="ov" @click.self="showViewModal = false">
      <div class="modal modal-lg">
        <div class="mdl-hd mhd-teal">
          <div class="mhd-left"><div class="mhd-ico">🔍</div><h2>Customer Ledger</h2></div>
          <button class="mdl-close" @click="showViewModal = false">✕</button>
        </div>
        <div class="mdl-body" v-if="selectedCustomer">
          <!-- Customer header -->
          <div class="v-hero">
            <div class="v-av" :style="{ background: avColor(selectedCustomer.name) }">{{ initials(selectedCustomer.name) }}</div>
            <div class="v-hero-text">
              <p class="v-name">{{ selectedCustomer.name }}</p>
              <p class="v-rel">{{ selectedCustomer.alternateName || 'No relation noted' }} · {{ selectedCustomer.mobile || 'No mobile' }}</p>
            </div>
            <span class="status-badge ml-auto" :class="selectedCustomer.totalBalance <= 0 ? 'sb-ok' : 'sb-due'">{{ selectedCustomer.totalBalance <= 0 ? 'Cleared' : 'Pending' }}</span>
          </div>

          <!-- Bill strip -->
          <div class="bill-strip">
            <div class="bs-col"><div class="bs-label">Total Billed</div><div class="bs-val" style="color:#DC2626">₹{{ fmt(selectedCustomer.totalDebit) }}</div></div>
            <div class="bs-op">−</div>
            <div class="bs-col"><div class="bs-label">Total Paid</div><div class="bs-val" style="color:#16A34A">₹{{ fmt(selectedCustomer.totalCredit) }}</div></div>
            <div class="bs-op">=</div>
            <div class="bs-col"><div class="bs-label">Balance</div><div class="bs-val bs-big" :style="{ color: selectedCustomer.totalBalance > 0 ? '#DC2626' : '#16A34A' }">₹{{ fmt(selectedCustomer.totalBalance) }}</div></div>
          </div>

          <!-- All transactions table -->
          <div style="margin-top:18px">
            <div style="display:flex;align-items:center;justify-content:space-between;margin-bottom:10px">
              <span style="font-size:13px;font-weight:600;color:#111827">All Transactions</span>
              <span class="txn-badge">{{ selectedCustomer.transactions.length }} transactions</span>
            </div>
            <div class="tbl-wrap" style="border:1px solid #F3F4F6;border-radius:10px;overflow:hidden">
              <table class="tbl" style="font-size:12.5px">
                <thead><tr>
                  <th>#</th><th>Date</th><th>Description</th><th>Bill No.</th><th>Book No.</th>
                  <th class="ra">Debit ₹</th><th class="ra">Credit ₹</th><th class="ra">Balance ₹</th>
                </tr></thead>
                <tbody>
                  <tr v-for="(t, i) in selectedCustomer.transactions" :key="t.id">
                    <td class="td-sl">{{ i + 1 }}</td>
                    <td class="td-dim">{{ fmtDate(t.date) }}</td>
                    <td>{{ t.description || '—' }}</td>
                    <td class="td-dim">{{ t.billNo || '—' }}</td>
                    <td class="td-dim">{{ t.bookNo || '—' }}</td>
                    <td class="ra td-red td-mono">{{ t.debit ? fmt(t.debit) : '—' }}</td>
                    <td class="ra td-grn td-mono">{{ t.credit ? fmt(t.credit) : '—' }}</td>
                    <td class="ra">
                      <span class="bal-pill" :class="((t.debit||0)-(t.credit||0)) > 0 ? 'bp-red' : 'bp-grn'" style="font-size:11px">
                        {{ fmt((t.debit||0)-(t.credit||0)) }}
                      </span>
                    </td>
                  </tr>
                  <tr style="background:#F8FAFC;font-weight:700;border-top:2px solid #E4E7EC">
                    <td colspan="5" style="padding:10px 14px;font-size:12px;color:#374151">Grand Total</td>
                    <td class="ra td-red td-mono" style="padding:10px 14px">{{ fmt(selectedCustomer.totalDebit) }}</td>
                    <td class="ra td-grn td-mono" style="padding:10px 14px">{{ fmt(selectedCustomer.totalCredit) }}</td>
                    <td class="ra" style="padding:10px 14px"><span class="bal-pill" :class="selectedCustomer.totalBalance > 0 ? 'bp-red' : 'bp-grn'">{{ fmt(selectedCustomer.totalBalance) }}</span></td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>
        </div>
        <div class="mdl-ft">
          <button class="btn-cancel" @click="showViewModal = false">Close</button>
          <button class="btn-ok bok-amber" @click="openPaymentModal(selectedCustomer); showViewModal = false">+ Add Transaction</button>
        </div>
      </div>
    </div>
    </Teleport>

    <!-- ══ ADD PAYMENT / QUICK CREDIT MODAL ══ -->
    <Teleport to="body">
    <div v-if="showPayModal" class="ov" @click.self="closePayModal">
      <div class="modal modal-sm">
        <div class="mdl-hd mhd-green">
          <div class="mhd-left"><div class="mhd-ico">💳</div><h2>Record Payment</h2></div>
          <button class="mdl-close" @click="closePayModal">✕</button>
        </div>
        <div class="mdl-body" v-if="selectedCustomer">
          <div class="pay-hero">
            <div class="v-av" :style="{ background: avColor(selectedCustomer.name) }">{{ initials(selectedCustomer.name) }}</div>
            <div><p class="v-name">{{ selectedCustomer.name }}</p><p class="v-rel">Outstanding: <strong style="color:#DC2626">₹{{ fmt(selectedCustomer.totalBalance) }}</strong></p></div>
          </div>
          <div class="fg" style="margin-top:18px"><label>Payment Amount (₹) <sup class="req">*</sup></label><input class="fi fi-mono" style="font-size:20px;padding:12px" v-model.number="payAmount" type="number" placeholder="0" /></div>
          <div class="fgrid" style="margin-top:12px">
            <div class="fg"><label>Date</label><input class="fi" v-model="payDate" type="date" /></div>
            <div class="fg"><label>Note</label><input class="fi" v-model="payNote" placeholder="Cash / UPI…" /></div>
          </div>
          <p v-if="payError" class="errmsg">⚠ {{ payError }}</p>
        </div>
        <div class="mdl-ft">
          <button class="btn-cancel" @click="closePayModal">Cancel</button>
          <button class="btn-ok bok-green" :disabled="isSubmitting" @click="submitPayment">
            <span v-if="isSubmitting" class="spn"></span><span v-else>Record Payment</span>
          </button>
        </div>
      </div>
    </div>
    </Teleport>

    <!-- ══ DELETE MODAL ══ -->
    <Teleport to="body">
    <div v-if="showDeleteModal" class="ov" @click.self="showDeleteModal = false">
      <div class="modal modal-xs">
        <div class="mdl-hd mhd-red">
          <div class="mhd-left"><div class="mhd-ico">🗑️</div><h2>Delete All Records</h2></div>
          <button class="mdl-close" @click="showDeleteModal = false">✕</button>
        </div>
        <div class="mdl-body">
          <p style="font-size:14.5px;color:#374151;line-height:1.6">Delete <strong>all {{ selectedCustomer?.transactions?.length }} transaction(s)</strong> for <strong>{{ selectedCustomer?.name }}</strong>?</p>
          <p style="font-size:13px;color:#9CA3AF;margin-top:6px">This action is permanent and cannot be undone.</p>
        </div>
        <div class="mdl-ft">
          <button class="btn-cancel" @click="showDeleteModal = false">Cancel</button>
          <button class="btn-ok bok-red" :disabled="isSubmitting" @click="deleteCustomer">
            <span v-if="isSubmitting" class="spn"></span><span v-else>Yes, Delete All</span>
          </button>
        </div>
      </div>
    </div>
    </Teleport>

    <!-- Toast -->
    <Transition name="toast-anim">
      <div v-if="toast.show" class="toast" :class="'toast-' + toast.type">
        <span class="toast-dot" :class="'td-' + toast.type"></span>
        {{ toast.message }}
      </div>
    </Transition>

  </div>
</template>

<script setup>
import { ref, computed, onMounted, reactive, nextTick } from 'vue'

const API_URL = 'https://script.google.com/macros/s/AKfycbxdTxeHMyfyiEFq6FH5MWYEclls2QEQwTSdKlne4LbCEknCObaHZ_bmKoJ85qRZqLQtrQ/exec'

/* ── state ── */
const rawRecords   = ref([])   // all raw rows from Google Sheet
const isLoading    = ref(true)
const isSubmitting = ref(false)
const activeTab    = ref('dashboard')

const filterName    = ref('')
const filterBalance = ref('')

const showFormModal   = ref(false)
const showViewModal   = ref(false)
const showPayModal    = ref(false)
const showDeleteModal = ref(false)

// addMode: 'search' | 'new' | 'new_txn'
const addMode         = ref('search')
const selectedCustomer = ref(null)
const matchedCustomer  = ref(null)  // the existing customer chosen for new_txn
const formError = ref('')
const payError  = ref('')

// Duplicate detection
const dupSearchQuery = ref('')
const dupResults     = ref([])
const dupSearching   = ref(false)
const dupSearchRef   = ref(null)

const emptyForm = () => ({
  id: null, name: '', alternateName: '', address: '', mobile: '',
  altMobile: '', date: new Date().toISOString().split('T')[0],
  description: '', billNo: '', bookNo: '', debit: 0, credit: 0
})
const form = reactive(emptyForm())

const payAmount = ref(0)
const payDate   = ref(new Date().toISOString().split('T')[0])
const payNote   = ref('')
const toast     = reactive({ show: false, message: '', type: 'success' })

/* ══════════════════════════════════════════════════
   CORE: group raw records into unique customers
   Key: mobile number (preferred) or normalised name
══════════════════════════════════════════════════ */
const uniqueCustomers = computed(() => {
  const map = new Map()

  for (const r of rawRecords.value) {
    // Build a stable key: mobile (trimmed) preferred, else UPPER name
    const mobile = (r.mobile || '').replace(/\s+/g, '').trim()
    const key    = mobile || (r.name || '').trim().toUpperCase()
    if (!key) continue

    if (!map.has(key)) {
      map.set(key, {
        key,
        name:          (r.name || '').trim().toUpperCase(),
        alternateName: r.alternateName || '',
        mobile:        r.mobile || '',
        altMobile:     r.altMobile || '',
        address:       r.address || '',
        transactions:  [],
        totalDebit:    0,
        totalCredit:   0,
        totalBalance:  0,
      })
    }

    const cust = map.get(key)
    // Update contact info from newest record (preserve non-empty)
    if (r.alternateName) cust.alternateName = r.alternateName
    if (r.address)       cust.address       = r.address
    if (r.altMobile)     cust.altMobile     = r.altMobile

    cust.transactions.push({ ...r })
    cust.totalDebit   += (Number(r.debit)  || 0)
    cust.totalCredit  += (Number(r.credit) || 0)
    cust.totalBalance  = cust.totalDebit - cust.totalCredit
  }

  return [...map.values()]
})

/* ── derived totals ── */
const allDebit   = computed(() => uniqueCustomers.value.reduce((s, c) => s + c.totalDebit,  0))
const allCredit  = computed(() => uniqueCustomers.value.reduce((s, c) => s + c.totalCredit, 0))
const allBalance = computed(() => allDebit.value - allCredit.value)
const allPending = computed(() => uniqueCustomers.value.filter(c => c.totalBalance > 0).length)

const pendingCustomers = computed(() =>
  uniqueCustomers.value.filter(c => c.totalBalance > 0).sort((a, b) => b.totalBalance - a.totalBalance)
)
const clearedCustomers = computed(() =>
  uniqueCustomers.value.filter(c => c.totalBalance <= 0)
)
const topDues = computed(() =>
  uniqueCustomers.value.filter(c => c.totalBalance > 0).sort((a, b) => b.totalBalance - a.totalBalance).slice(0, 10)
)
const collectionPct = computed(() => {
  if (!allDebit.value) return 0
  return Math.round((allCredit.value / allDebit.value) * 100)
})

/* ── filtered for customers tab ── */
const filteredUniqueCustomers = computed(() => {
  const q = filterName.value.trim().toLowerCase()
  return uniqueCustomers.value.filter(c => {
    if (q) {
      const name   = c.name.toLowerCase()
      const mobile = (c.mobile || '').toLowerCase()
      const alt    = (c.alternateName || '').toLowerCase()
      if (!name.includes(q) && !mobile.includes(q) && !alt.includes(q)) return false
    }
    if (filterBalance.value === 'pending'  && c.totalBalance <= 0) return false
    if (filterBalance.value === 'cleared'  && c.totalBalance > 0)  return false
    return true
  })
})

/* ── new running balance preview in form ── */
const newRunningBalance = computed(() => {
  const prevBal = (addMode.value === 'new_txn' && matchedCustomer.value)
    ? matchedCustomer.value.totalBalance
    : 0
  return prevBal + (form.debit || 0) - (form.credit || 0)
})

/* ── helpers ── */
const AV_COLORS = ['#6366F1','#8B5CF6','#EC4899','#F59E0B','#10B981','#3B82F6','#EF4444','#14B8A6','#F97316']
const avColor  = name => AV_COLORS[(name ? name.charCodeAt(0) : 0) % AV_COLORS.length]
const fmt      = n => Number(n || 0).toLocaleString('en-IN')
const initials = name => name ? name.split(' ').slice(0, 2).map(w => w[0]).join('').toUpperCase() : '?'
const fmtDate  = d => {
  if (!d) return '—'
  const dt = new Date(d)
  return isNaN(dt) ? d : dt.toLocaleDateString('en-IN', { day: '2-digit', month: 'short', year: 'numeric' })
}
function showToast(msg, type = 'success') {
  Object.assign(toast, { show: true, message: msg, type })
  setTimeout(() => { toast.show = false }, 3000)
}
const clearFilters = () => { filterName.value = ''; filterBalance.value = '' }

/* ── api ── */
async function loadCustomers() {
  isLoading.value = true
  try {
    const res  = await fetch(API_URL, { method: 'POST', body: JSON.stringify({ action: 'getAll' }) })
    const data = await res.json()
    rawRecords.value = (data.records || []).map(r => ({
      ...r,
      debit:  Number(r.debit)  || 0,
      credit: Number(r.credit) || 0,
    }))
  } catch { showToast('Failed to load data', 'error') }
  finally { isLoading.value = false }
}

/* ══ Duplicate detection logic ══ */
function onDupSearch() {
  const q = dupSearchQuery.value.trim().toLowerCase()
  if (q.length < 2) { dupResults.value = []; return }

  dupSearching.value = true
  dupResults.value = uniqueCustomers.value.filter(c => {
    const name   = c.name.toLowerCase()
    const mobile = (c.mobile || '').replace(/\s+/g, '')
    const alt    = (c.alternateName || '').toLowerCase()
    return name.includes(q) || mobile.includes(q.replace(/\s+/g, '')) || alt.includes(q)
  })
  dupSearching.value = false
}

function onMobileInput() {
  // Auto-trigger duplicate check when mobile is typed in the 'new' form
  const q = (form.mobile || '').replace(/\s+/g, '').trim()
  if (q.length >= 6) {
    const match = uniqueCustomers.value.find(c =>
      (c.mobile || '').replace(/\s+/g, '') === q
    )
    if (match) {
      // Automatically switch to new_txn mode for this customer
      selectExistingCustomer(match)
    }
  }
}

function selectExistingCustomer(customer) {
  matchedCustomer.value = customer
  addMode.value = 'new_txn'
  dupSearchQuery.value = ''
  dupResults.value = []
  // Pre-fill transaction form
  Object.assign(form, emptyForm())
  form.date = new Date().toISOString().split('T')[0]
}

function prefillFormFromSearch() {
  addMode.value = 'new'
  // Try to fill mobile / name from search query
  const q = dupSearchQuery.value.trim()
  if (/^\d+$/.test(q)) form.mobile = q
  else form.name = q.toUpperCase()
  dupSearchQuery.value = ''
  dupResults.value = []
}

/* ── modal openers ── */
function openAddModal() {
  addMode.value = 'search'
  matchedCustomer.value = null
  dupSearchQuery.value = ''
  dupResults.value = []
  Object.assign(form, emptyForm())
  formError.value = ''
  showFormModal.value = true
  nextTick(() => dupSearchRef.value?.focus())
}

function openViewModal(c) {
  selectedCustomer.value = c
  showViewModal.value = true
}

function openPaymentModal(c) {
  selectedCustomer.value = c
  payAmount.value = 0
  payDate.value   = new Date().toISOString().split('T')[0]
  payNote.value   = ''
  payError.value  = ''
  showPayModal.value = true
}

function confirmDelete(c) {
  selectedCustomer.value = c
  showDeleteModal.value = true
}

const closeFormModal = () => { showFormModal.value = false }
const closePayModal  = () => { showPayModal.value  = false }

/* ── CRUD ── */
async function submitForm() {
  if (addMode.value === 'new') {
    if (!form.name.trim())   { formError.value = 'Name is required';        return }
    if (!form.mobile.trim()) { formError.value = 'Mobile is required';      return }
    if (!form.debit)         { formError.value = 'Total bill is required';  return }
  }
  if (addMode.value === 'new_txn') {
    if (!form.debit) { formError.value = 'Total bill is required'; return }
  }
  formError.value  = ''
  isSubmitting.value = true

  try {
    let payload

    if (addMode.value === 'new') {
      // Brand new customer + first transaction
      payload = {
        action: 'add',
        record: {
          ...form,
          name:         form.name.trim().toUpperCase(),
          totalBalance: (form.debit || 0) - (form.credit || 0),
        }
      }
    } else {
      // New transaction for existing customer — keep same mobile/name
      payload = {
        action: 'add',
        record: {
          name:          matchedCustomer.value.name,
          alternateName: matchedCustomer.value.alternateName,
          mobile:        matchedCustomer.value.mobile,
          altMobile:     matchedCustomer.value.altMobile,
          address:       matchedCustomer.value.address,
          date:          form.date,
          billNo:        form.billNo,
          bookNo:        form.bookNo,
          description:   form.description,
          debit:         form.debit  || 0,
          credit:        form.credit || 0,
          totalBalance:  (form.debit || 0) - (form.credit || 0),
        }
      }
    }

    const res  = await fetch(API_URL, { method: 'POST', body: JSON.stringify(payload) })
    const data = await res.json()

    if (data.success) {
      showToast(addMode.value === 'new_txn' ? 'Transaction added!' : 'Customer added!')
      closeFormModal()
      await loadCustomers()
    } else {
      formError.value = data.error || 'Something went wrong'
    }
  } catch { formError.value = 'Network error' }
  finally  { isSubmitting.value = false }
}

async function submitPayment() {
  if (!payAmount.value || payAmount.value <= 0) { payError.value = 'Enter a valid amount'; return }
  payError.value     = ''
  isSubmitting.value = true
  try {
    const res  = await fetch(API_URL, { method: 'POST', body: JSON.stringify({
      action: 'addPayment',
      record: {
        id:     selectedCustomer.value.transactions[0]?.id, // use first txn id as reference
        amount: payAmount.value,
        date:   payDate.value,
        note:   payNote.value,
        mobile: selectedCustomer.value.mobile,
        name:   selectedCustomer.value.name,
      }
    }) })
    const data = await res.json()
    if (data.success) { showToast('Payment recorded!'); closePayModal(); await loadCustomers() }
    else payError.value = data.error || 'Something went wrong'
  } catch { payError.value = 'Network error' }
  finally  { isSubmitting.value = false }
}

async function deleteCustomer() {
  isSubmitting.value = true
  try {
    // Delete all transaction rows for this customer
    const ids = selectedCustomer.value.transactions.map(t => t.id).filter(Boolean)
    const res  = await fetch(API_URL, { method: 'POST', body: JSON.stringify({ action: 'deleteMany', ids }) })
    const data = await res.json()
    if (data.success) { showToast('All records deleted'); showDeleteModal.value = false; await loadCustomers() }
    else showToast('Delete failed', 'error')
  } catch { showToast('Delete failed', 'error') }
  finally  { isSubmitting.value = false }
}
function sendWhatsAppReminder(c) {
  const bal = fmt(c.totalBalance)
  const msg =
    `Namaskar ${c.name} Ji 🙏\n\n` +
    `Ye Shree Renuka SBT Management ki taraf se ek vinay hai.\n\n` +
    `Aapka account mein *₹${bal}* outstanding balance pending hai.\n\n` +
    `Kripya jaldi se payment karein.\n\n` +
    `Dhanyawad 🙏\n— Shree Renuka Team`

  const mobile = (c.mobile || '').replace(/\D/g, '')
  const url = mobile
    ? `https://wa.me/91${mobile}?text=${encodeURIComponent(msg)}`
    : `https://wa.me/?text=${encodeURIComponent(msg)}`

  window.open(url, '_blank')
}

onMounted(loadCustomers)
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&family=JetBrains+Mono:wght@400;500&display=swap');
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
html, body, #app { height: 100%; font-family: 'Inter', system-ui, sans-serif; background: #F0F2F5; color: #1A1D23; }

.layout { display: flex; min-height: 100vh; }

/* ── Sidebar ── */
.sidebar { width: 220px; min-height: 100vh; background: #1C2333; flex-shrink: 0; display: flex; flex-direction: column; }
.sb-brand { padding: 20px 18px; border-bottom: 1px solid #273047; display: flex; align-items: center; gap: 10px; }
.sb-brand-icon { width: 38px; height: 38px; border-radius: 9px; display: flex; align-items: center; justify-content: center; font-size: 18px; flex-shrink: 0; }
.sb-brand-name { font-size: 14px; font-weight: 700; color: #F8FAFC; line-height: 1.2; }
.sb-brand-sub  { font-size: 11px; color: #64748B; }
.sb-nav { padding: 14px 10px; flex: 1; }
.sb-item { display: flex; align-items: center; gap: 10px; padding: 9px 12px; border-radius: 7px; font-size: 13px; font-weight: 500; color: #64748B; cursor: pointer; margin-bottom: 2px; transition: all .15s; user-select: none; }
.sb-item:hover:not(.sb-active) { background: #273047; color: #94A3B8; }
.sb-active { background: #1E3A5F !important; color: #60A5FA !important; font-weight: 600; }
.sb-bottom { padding: 14px 10px 20px; border-top: 1px solid #273047; }
.sb-section-label { font-size: 10px; font-weight: 600; text-transform: uppercase; letter-spacing: .6px; color: #475569; margin-bottom: 8px; padding: 0 2px; }
.sb-stat-card { background: #131B2E; border-radius: 9px; padding: 12px 14px; }
.sb-st-row  { display: flex; justify-content: space-between; align-items: center; padding: 4px 0; }
.sb-st-label { font-size: 11.5px; color: #475569; }
.sb-st-val   { font-family: 'JetBrains Mono', monospace; font-size: 13px; font-weight: 600; }
.sb-divider  { border-top: 1px solid #273047; margin: 6px 0; }

/* ── Main ── */
.main { flex: 1; background: #F0F2F5; display: flex; flex-direction: column; overflow-x: hidden; }
.topbar { background: #FFFFFF; border-bottom: 1px solid #E4E7EC; padding: 16px 24px; display: flex; align-items: center; justify-content: space-between; }
.topbar-title { font-size: 18px; font-weight: 700; color: #0F172A; }
.topbar-sub   { font-size: 12px; color: #9CA3AF; margin-top: 2px; }
.btn-add { display: inline-flex; align-items: center; gap: 7px; padding: 10px 18px; background: #2563EB; color: #fff; border: none; border-radius: 8px; font-family: 'Inter', sans-serif; font-size: 13.5px; font-weight: 600; cursor: pointer; transition: background .15s; }
.btn-add:hover { background: #1D4ED8; }
.link-btn { background: none; border: none; color: #2563EB; font-family: 'Inter', sans-serif; font-size: 13px; font-weight: 600; cursor: pointer; }
.link-btn:hover { text-decoration: underline; }

/* ── Chips ── */
.chips { display: grid; grid-template-columns: repeat(4,1fr); gap: 14px; padding: 20px 24px 0; }
.chip { background: #FFFFFF; border: 1px solid #E4E7EC; border-radius: 10px; padding: 16px 18px; }
.chip-top { display: flex; justify-content: space-between; align-items: flex-start; margin-bottom: 10px; }
.chip-icon { width: 38px; height: 38px; border-radius: 9px; display: flex; align-items: center; justify-content: center; font-size: 16px; flex-shrink: 0; }
.chip-num  { font-family: 'JetBrains Mono', monospace; font-size: 20px; font-weight: 700; line-height: 1; margin-bottom: 4px; }
.chip-lbl  { font-size: 11px; color: #9CA3AF; font-weight: 500; text-transform: uppercase; letter-spacing: .5px; }
.chip-note { font-size: 11.5px; font-weight: 600; }

/* ── Filter bar ── */
.filter-bar { display: flex; align-items: center; gap: 10px; flex-wrap: wrap; padding: 14px 24px; background: #FFFFFF; border-bottom: 1px solid #E4E7EC; margin-top: 16px; }
.search-box { position: relative; flex: 1; min-width: 180px; }
.search-ico { position: absolute; left: 11px; top: 50%; transform: translateY(-50%); color: #9CA3AF; pointer-events: none; }
.search-inp { width: 100%; padding: 9px 12px 9px 33px; background: #F9FAFB; border: 1px solid #E4E7EC; border-radius: 8px; font-family: 'Inter', sans-serif; font-size: 13px; color: #1A1D23; outline: none; transition: border-color .15s; }
.search-inp:focus { border-color: #2563EB; background: #fff; }
.search-inp::placeholder { color: #D1D5DB; }
.f-select   { padding: 9px 12px; background: #F9FAFB; border: 1px solid #E4E7EC; border-radius: 8px; font-family: 'Inter', sans-serif; font-size: 13px; color: #374151; outline: none; cursor: pointer; }
.btn-clear  { padding: 9px 14px; background: transparent; border: 1px solid #E4E7EC; border-radius: 8px; font-family: 'Inter', sans-serif; font-size: 13px; color: #9CA3AF; cursor: pointer; transition: all .15s; }
.btn-clear:hover { border-color: #DC2626; color: #DC2626; }

/* ── States ── */
.state-center { display: flex; flex-direction: column; align-items: center; justify-content: center; gap: 10px; padding: 80px; color: #9CA3AF; }
.loader-ring  { width: 34px; height: 34px; border: 3px solid #E4E7EC; border-top-color: #2563EB; border-radius: 50%; animation: spin .7s linear infinite; }
@keyframes spin { to { transform: rotate(360deg); } }

/* ── Table ── */
.tbl-card { margin: 16px 24px 0; background: #FFFFFF; border: 1px solid #E4E7EC; border-radius: 12px; overflow: hidden; }
.tbl-card:last-child { margin-bottom: 28px; }
.tbl-header { padding: 14px 18px; border-bottom: 1px solid #F1F5F9; display: flex; align-items: center; justify-content: space-between; }
.tbl-title  { font-size: 14px; font-weight: 600; color: #111827; }
.tbl-badge  { background: #EFF6FF; color: #2563EB; font-size: 11.5px; font-weight: 600; padding: 3px 10px; border-radius: 20px; }
.tbl-wrap   { overflow-x: auto; }
.tbl { width: 100%; border-collapse: collapse; font-size: 13px; }
.tbl thead tr { background: #F8FAFC; }
.tbl th { padding: 10px 14px; text-align: left; font-size: 11px; font-weight: 600; text-transform: uppercase; letter-spacing: .5px; color: #9CA3AF; white-space: nowrap; }
.tbl th.ra, .tbl td.ra { text-align: right; }
.tbl tbody tr { border-top: 1px solid #F3F4F6; transition: background .1s; }
.tbl tbody tr:hover td { background: #FAFBFF; }
.tbl tbody tr.tr-cleared { opacity: .5; }
.tbl td { padding: 11px 14px; vertical-align: middle; }
.td-sl   { color: #D1D5DB; font-size: 12px; text-align: center; }
.td-dim  { color: #9CA3AF; }
.td-mono { font-family: 'JetBrains Mono', monospace; }
.td-red  { color: #DC2626; }
.td-grn  { color: #16A34A; }
.name-cell { display: flex; align-items: center; gap: 9px; }
.av { width: 28px; height: 28px; border-radius: 50%; color: #fff; font-size: 10px; font-weight: 700; display: inline-flex; align-items: center; justify-content: center; flex-shrink: 0; }
.name-txt { font-weight: 600; color: #111827; white-space: nowrap; }
.bal-pill { display: inline-block; padding: 2px 9px; border-radius: 5px; font-family: 'JetBrains Mono', monospace; font-size: 12px; font-weight: 600; }
.bp-red  { background: #FEF2F2; color: #DC2626; }
.bp-grn  { background: #F0FDF4; color: #16A34A; }
.status-badge { display: inline-flex; align-items: center; gap: 5px; padding: 3px 9px; border-radius: 20px; font-size: 11px; font-weight: 600; }
.status-badge::before { content: ''; width: 5px; height: 5px; border-radius: 50%; flex-shrink: 0; }
.sb-due { background: #FEF2F2; color: #B91C1C; } .sb-due::before { background: #EF4444; }
.sb-ok  { background: #F0FDF4; color: #15803D; } .sb-ok::before  { background: #22C55E; }
.ml-auto { margin-left: auto; }
.acts { display: flex; gap: 4px; }
.ab { width: 27px; height: 27px; border-radius: 6px; border: 1px solid #E4E7EC; background: #fff; display: flex; align-items: center; justify-content: center; cursor: pointer; color: #9CA3AF; transition: all .15s; }
.ab:hover { transform: scale(1.1); }
.ab-v:hover { background: #F0FDFA; border-color: #0D9488; color: #0D9488; }
.ab-p:hover { background: #F0FDF4; border-color: #16A34A; color: #16A34A; }
.ab-e:hover { background: #EFF6FF; border-color: #2563EB; color: #2563EB; }
.ab-d:hover { background: #FEF2F2; border-color: #DC2626; color: #DC2626; }
.btn-pay-now { padding: 5px 12px; background: #F0FDF4; border: 1px solid #BBF7D0; border-radius: 6px; color: #16A34A; font-family: 'Inter', sans-serif; font-size: 12px; font-weight: 600; cursor: pointer; transition: all .15s; white-space: nowrap; }
.btn-pay-now:hover { background: #16A34A; color: #fff; }

/* ── Transaction badge ── */
.txn-badge { background: #EFF6FF; color: #2563EB; font-size: 11px; font-weight: 600; padding: 2px 8px; border-radius: 12px; white-space: nowrap; }

/* ── Duplicate search UI ── */
.dup-search-wrap { background: #F8FAFC; border: 1.5px solid #E4E7EC; border-radius: 10px; padding: 14px 16px; margin-bottom: 4px; }
.dup-search-label { font-size: 11px; font-weight: 600; color: #9CA3AF; text-transform: uppercase; letter-spacing: .6px; margin-bottom: 10px; }
.dup-search-row { display: flex; gap: 10px; }
.dup-dropdown { margin-top: 10px; border: 1px solid #E4E7EC; border-radius: 10px; overflow: hidden; background: #fff; box-shadow: 0 4px 16px rgba(0,0,0,.08); }
.dup-dropdown-label { padding: 8px 14px; font-size: 11px; font-weight: 600; color: #6B7280; background: #F8FAFC; border-bottom: 1px solid #F3F4F6; text-transform: uppercase; letter-spacing: .5px; }
.dup-result-item { display: flex; align-items: center; gap: 12px; padding: 12px 14px; cursor: pointer; transition: background .12s; border-bottom: 1px solid #F3F4F6; }
.dup-result-item:last-of-type { border-bottom: none; }
.dup-result-item:hover { background: #EFF6FF; }
.dup-result-info { flex: 1; }
.dup-result-info strong { display: block; font-size: 13.5px; color: #111827; font-weight: 600; }
.dup-result-info span  { font-size: 12px; color: #9CA3AF; margin-top: 2px; display: block; }
.dup-or-new { display: flex; align-items: center; gap: 8px; padding: 10px 14px; font-size: 12.5px; color: #2563EB; font-weight: 600; cursor: pointer; background: #F8FAFC; border-top: 1px solid #E4E7EC; transition: background .12s; }
.dup-or-new:hover { background: #EFF6FF; }
.dup-no-result { font-size: 13px; color: #9CA3AF; margin-top: 10px; padding: 8px 12px; background: #FFFBEB; border: 1px solid #FDE68A; border-radius: 8px; }
.dup-new-link { color: #2563EB; font-weight: 600; cursor: pointer; text-decoration: underline; }

/* ── Existing transaction summary in modal ── */
.existing-txn-summary { margin: 14px 0 0; border: 1.5px solid #FDE68A; border-radius: 10px; overflow: hidden; }
.ets-header { display: flex; align-items: center; justify-content: space-between; padding: 10px 14px; background: #FFFBEB; border-bottom: 1px solid #FDE68A; font-size: 12px; font-weight: 600; color: #92400E; }
.ets-table-wrap { overflow-x: auto; max-height: 200px; overflow-y: auto; }

/* ── Reports ── */
.report-grid { display: grid; grid-template-columns: repeat(3,1fr); gap: 14px; padding: 20px 24px 0; }
.report-card { border-radius: 12px; padding: 20px 22px; display: flex; flex-direction: column; gap: 4px; border: 1px solid transparent; }
.rc-icon { font-size: 22px; margin-bottom: 6px; }
.rc-val  { font-family: 'JetBrains Mono', monospace; font-size: 22px; font-weight: 700; line-height: 1; }
.rc-lbl  { font-size: 12px; font-weight: 500; text-transform: uppercase; letter-spacing: .5px; opacity: .75; }
.rc-blue   { background: #EFF6FF; border-color: #BFDBFE; } .rc-blue .rc-val { color: #1D4ED8; } .rc-blue .rc-lbl { color: #3B82F6; }
.rc-red    { background: #FEF2F2; border-color: #FECACA; } .rc-red .rc-val  { color: #B91C1C; } .rc-red .rc-lbl  { color: #EF4444; }
.rc-green  { background: #F0FDF4; border-color: #BBF7D0; } .rc-green .rc-val { color: #15803D; } .rc-green .rc-lbl { color: #22C55E; }
.rc-amber  { background: #FFFBEB; border-color: #FDE68A; } .rc-amber .rc-val { color: #B45309; } .rc-amber .rc-lbl { color: #F59E0B; }
.rc-purple { background: #F5F3FF; border-color: #DDD6FE; } .rc-purple .rc-val { color: #6D28D9; } .rc-purple .rc-lbl { color: #8B5CF6; }
.rc-teal   { background: #F0FDFA; border-color: #99F6E4; } .rc-teal .rc-val { color: #0F766E; } .rc-teal .rc-lbl { color: #14B8A6; }
.progress-track { height: 12px; background: #F3F4F6; border-radius: 99px; overflow: hidden; }
.progress-fill  { height: 100%; background: linear-gradient(90deg,#22C55E,#16A34A); border-radius: 99px; transition: width .6s ease; }

/* ── Modal ── */
.ov { position: fixed; inset: 0; background: rgba(0,0,0,.45); backdrop-filter: blur(3px); display: flex; align-items: center; justify-content: center; z-index: 1000; padding: 20px; }
.modal { background: #FFFFFF; border-radius: 14px; border: 1px solid #E4E7EC; box-shadow: 0 20px 60px rgba(0,0,0,.18); width: 100%; display: flex; flex-direction: column; max-height: 92vh; overflow: hidden; animation: mIn .22s cubic-bezier(.22,1,.36,1); }
@keyframes mIn { from{opacity:0;transform:translateY(14px) scale(.97)} to{opacity:1;transform:none} }
.modal-xs { max-width:380px; } .modal-sm { max-width:440px; } .modal-md { max-width:560px; } .modal-lg { max-width: 91%;
    min-height: 90vh; }
.mdl-hd { display:flex; align-items:center; justify-content:space-between; padding:18px 22px; flex-shrink:0; }
.mhd-blue  { background:#EFF6FF; border-bottom:2px solid #BFDBFE; }
.mhd-amber { background:#FFFBEB; border-bottom:2px solid #FDE68A; }
.mhd-teal  { background:#F0FDFA; border-bottom:2px solid #99F6E4; }
.mhd-green { background:#F0FDF4; border-bottom:2px solid #BBF7D0; }
.mhd-red   { background:#FEF2F2; border-bottom:2px solid #FECACA; }
.mhd-left  { display:flex; align-items:center; gap:12px; }
.mhd-ico   { font-size:20px; }
.mdl-hd h2 { font-size:16px; font-weight:700; color:#111827; }
.mdl-close { width:30px; height:30px; border-radius:7px; border:1px solid #E4E7EC; background:transparent; cursor:pointer; font-size:13px; color:#9CA3AF; display:flex; align-items:center; justify-content:center; transition:all .15s; }
.mdl-close:hover { background:#FEF2F2; border-color:#FECACA; color:#DC2626; }
.mdl-body { padding:20px 22px; overflow-y:auto; flex:1; }
.mdl-ft   { display:flex; justify-content:flex-end; gap:10px; padding:14px 22px 18px; border-top:1px solid #F3F4F6; flex-shrink:0; }
.btn-cancel { padding:9px 18px; background:transparent; border:1px solid #E4E7EC; border-radius:8px; font-family:'Inter',sans-serif; font-size:13.5px; color:#6B7280; cursor:pointer; transition:all .15s; }
.btn-cancel:hover { border-color:#9CA3AF; color:#374151; }
.btn-ok { padding:9px 22px; border:none; border-radius:8px; font-family:'Inter',sans-serif; font-size:13.5px; font-weight:600; color:#fff; cursor:pointer; display:flex; align-items:center; gap:7px; transition:opacity .15s; }
.btn-ok:disabled { opacity:.5; cursor:not-allowed; }
.btn-ok:hover:not(:disabled) { opacity:.88; }
.bok-blue{background:#2563EB;} .bok-amber{background:#D97706;} .bok-teal{background:#0D9488;} .bok-green{background:#16A34A;} .bok-red{background:#DC2626;}
.fsec-title { font-size:11px; font-weight:600; text-transform:uppercase; letter-spacing:.6px; color:#9CA3AF; margin-bottom:12px; }
.mt20 { margin-top:20px; }
.fgrid { display:grid; grid-template-columns:1fr 1fr; gap:12px; }
.fg    { display:flex; flex-direction:column; gap:5px; }
.col2  { grid-column:1/-1; }
.fg label { font-size:12px; font-weight:500; color:#374151; }
.req  { color:#DC2626; font-style:normal; }
.fi   { padding:9px 12px; background:#F9FAFB; border:1px solid #E4E7EC; border-radius:8px; font-family:'Inter',sans-serif; font-size:13.5px; color:#111827; outline:none; transition:border-color .15s; width:100%; }
.fi:focus { border-color:#2563EB; background:#fff; box-shadow:0 0 0 3px rgba(37,99,235,.07); }
.fi::placeholder { color:#D1D5DB; }
.fi-mono { font-family:'JetBrains Mono',monospace; }
.bal-preview { margin-top:16px; background:#F8FAFC; border:1px solid #E4E7EC; border-radius:9px; padding:14px 16px; }
.bp-row   { display:flex; justify-content:space-between; align-items:center; padding:4px 0; font-size:13px; color:#6B7280; }
.bp-sep   { border-top:1px dashed #E4E7EC; margin:8px 0; }
.bp-total { font-weight:700; color:#111827; }
.errmsg   { color:#DC2626; font-size:12.5px; margin-top:10px; }
.v-hero { display:flex; align-items:center; gap:14px; padding-bottom:16px; border-bottom:1px solid #F3F4F6; margin-bottom:16px; }
.v-av   { width:46px; height:46px; border-radius:50%; color:#fff; font-size:15px; font-weight:700; display:flex; align-items:center; justify-content:center; flex-shrink:0; }
.v-hero-text { flex:1; }
.v-name { font-size:16px; font-weight:700; color:#111827; }
.v-rel  { font-size:12.5px; color:#9CA3AF; margin-top:2px; }
.bill-strip { display:flex; align-items:center; gap:10px; background:#F8FAFC; border:1px solid #E4E7EC; border-radius:9px; padding:14px 18px; }
.bs-col   { display:flex; flex-direction:column; gap:3px; flex:1; text-align:center; }
.bs-label { font-size:10.5px; color:#9CA3AF; text-transform:uppercase; letter-spacing:.5px; }
.bs-val   { font-family:'JetBrains Mono',monospace; font-size:15px; font-weight:700; }
.bs-big   { font-size:19px !important; }
.bs-op    { color:#D1D5DB; font-size:18px; }
.pay-hero { display:flex; align-items:center; gap:12px; padding-bottom:14px; border-bottom:1px solid #F3F4F6; }
.spn { display:inline-block; width:13px; height:13px; border:2px solid rgba(255,255,255,.3); border-top-color:#fff; border-radius:50%; animation:spin .65s linear infinite; }
.toast { position:fixed; bottom:24px; left:50%; transform:translateX(-50%); padding:11px 20px; border-radius:10px; font-size:13.5px; font-weight:500; display:flex; align-items:center; gap:10px; background:#111827; color:#F9FAFB; box-shadow:0 8px 30px rgba(0,0,0,.2); z-index:9999; white-space:nowrap; }
.toast-dot { width:8px; height:8px; border-radius:50%; flex-shrink:0; }
.td-success { background:#22C55E; } .td-error { background:#EF4444; }
.toast-anim-enter-active,.toast-anim-leave-active { transition:all .3s cubic-bezier(.22,1,.36,1); }
.toast-anim-enter-from,.toast-anim-leave-to { opacity:0; transform:translateX(-50%) translateY(10px); }
::-webkit-scrollbar { width:5px; height:5px; }
::-webkit-scrollbar-track { background:transparent; }
::-webkit-scrollbar-thumb { background:#E4E7EC; border-radius:3px; }
@media (max-width:900px) { .sidebar{display:none;} .chips{grid-template-columns:1fr 1fr;} .report-grid{grid-template-columns:1fr 1fr;} .chips,.filter-bar,.topbar{padding-left:16px;padding-right:16px;} .tbl-card{margin-left:16px;margin-right:16px;} }
@media (max-width:520px)  { .chips{grid-template-columns:1fr 1fr;gap:10px;} .fgrid{grid-template-columns:1fr;} .col2{grid-column:1;} }
</style>