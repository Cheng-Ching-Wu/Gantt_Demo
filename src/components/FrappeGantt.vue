<template>
    <div class="frappe-wrapper">
        <h3>高雄港加油預定表（Day view）</h3>
        <div class="gantt-content">
            <div ref="gantt2"></div>
        </div>

        <h3 style="margin-top:32px">船舶加油計畫表（Quarter Day view）</h3>
        <div class="gantt-content">
            <div ref="gantt1"></div>
        </div>

        <div class="limitation-note">
            ⚠️ FrappeGantt 限制：無左側 grid 分群、無 rowspan section label、
            同一列無法顯示多個 task、無封鎖時段遮罩、無空白列。
        </div>
    </div>
</template>

<script>
import Gantt from 'frappe-gantt'

export default {
    name: 'FrappeGantt',
    mounted() {
        this.initChart2()
        this.initChart1()
    },
    methods: {
        initChart2() {
            // ── 高雄港加油預定表：Day view ──
            // 限制：無 section 分群，以 "區域+編號" 作為 task 名稱代替
            const tasks = [
                // 碼頭
                { id: 'w1', name: '碼頭1  A船MFO70(L)',          start: '2025-10-01', end: '2025-10-02', progress: 0, custom_class: 'bar-blue' },
                { id: 'w2', name: '碼頭2  B船MFO50(L)',          start: '2025-10-01', end: '2025-10-02', progress: 0, custom_class: 'bar-blue' },
                { id: 'w3', name: '碼頭3  C船MFO45(L)',          start: '2025-10-01', end: '2025-10-02', progress: 0, custom_class: 'bar-blue' },
                { id: 'w4', name: '碼頭4  D船MFO200(H)MGO50',   start: '2025-10-04', end: '2025-10-06', progress: 0, custom_class: 'bar-blue' },
                { id: 'w5', name: '碼頭5  E船MFO40(L)',          start: '2025-10-04', end: '2025-10-05', progress: 0, custom_class: 'bar-blue' },
                { id: 'w6', name: '碼頭6  F船MFO150(L)',         start: '2025-10-07', end: '2025-10-09', progress: 0, custom_class: 'bar-blue' },
                { id: 'w7', name: '碼頭7  G船MFO55(L)',          start: '2025-10-08', end: '2025-10-09', progress: 0, custom_class: 'bar-blue' },
                // 浮桶
                { id: 'b1', name: '浮桶1  H船MFO225(L)MGO40',   start: '2025-10-01', end: '2025-10-09', progress: 0, custom_class: 'bar-orange' },
                { id: 'b2', name: '浮桶2  I船MFO125(L)',         start: '2025-10-04', end: '2025-10-09', progress: 0, custom_class: 'bar-orange' },
                // 錨地
                { id: 'a1', name: '錨地1  J船MFO2,000(H)',       start: '2025-10-01', end: '2025-10-02', progress: 0, custom_class: 'bar-pink' },
                { id: 'a2', name: '錨地2  K船MFO3,000(H)',       start: '2025-10-02', end: '2025-10-04', progress: 0, custom_class: 'bar-outline-blue' },
                { id: 'a3', name: '錨地3  L船MFO2,500(H)',       start: '2025-10-02', end: '2025-10-05', progress: 0, custom_class: 'bar-outline-blue' },
                { id: 'a4', name: '錨地4  M船MFO2,700(H)',       start: '2025-10-04', end: '2025-10-07', progress: 0, custom_class: 'bar-outline-pink' },
                { id: 'a5', name: '錨地5  N船MFO2,000(H)',       start: '2025-10-05', end: '2025-10-09', progress: 0, custom_class: 'bar-outline-blue' },
                // 油駁船
                { id: 't1', name: '油駁船1',                      start: '2025-10-01', end: '2025-10-01', progress: 0, custom_class: 'bar-red' },
            ]
            new Gantt(this.$refs.gantt2, tasks, {
                view_mode: 'Day',
                date_format: 'YYYY-MM-DD',
                show_expected_progress: false,
            })
        },

        initChart1() {
            // ── 船舶加油計畫表：Quarter Day（6hr）view ──
            // 限制：同一列（如「補油」）無法同時顯示多艘船，只能各佔一列
            const tasks = [
                // 油駁船1
                { id: 's1_j_bunkering', name: '油駁1-補油  J船', start: '2025-09-30 06:00:00', end: '2025-09-30 18:00:00', progress: 0, custom_class: 'bar-outline-navy' },
                { id: 's1_j_transfer',  name: '油駁1-駁油  J船', start: '2025-10-01 00:00:00', end: '2025-10-02 06:00:00', progress: 0, custom_class: 'bar-outline-red' },
                { id: 's1_k_bunkering', name: '油駁1-補油  K船', start: '2025-10-02 06:00:00', end: '2025-10-02 18:00:00', progress: 0, custom_class: 'bar-outline-navy' },
                { id: 's1_k_transfer',  name: '油駁1-駁油  K船', start: '2025-10-03 00:00:00', end: '2025-10-04 06:00:00', progress: 0, custom_class: 'bar-outline-red' },
                // 油駁船2
                { id: 's2_abc',  name: '油駁2-補油  A/B/C', start: '2025-09-30 00:00:00', end: '2025-09-30 12:00:00', progress: 0, custom_class: 'bar-outline-navy' },
                { id: 's2_dlh',  name: '油駁2-補油  D/L/H', start: '2025-10-02 06:00:00', end: '2025-10-02 18:00:00', progress: 0, custom_class: 'bar-outline-navy' },
                { id: 's2_a',    name: '油駁2-駁油  A',     start: '2025-10-01 00:00:00', end: '2025-10-01 06:00:00', progress: 0, custom_class: 'bar-outline-red' },
                { id: 's2_c',    name: '油駁2-駁油  C',     start: '2025-10-01 06:00:00', end: '2025-10-01 12:00:00', progress: 0, custom_class: 'bar-outline-red' },
                { id: 's2_b',    name: '油駁2-駁油  B',     start: '2025-10-01 12:00:00', end: '2025-10-02 00:00:00', progress: 0, custom_class: 'bar-outline-red' },
                { id: 's2_h',    name: '油駁2-駁油  H',     start: '2025-10-03 06:00:00', end: '2025-10-03 18:00:00', progress: 0, custom_class: 'bar-outline-red' },
                { id: 's2_l',    name: '油駁2-駁油  L船',   start: '2025-10-04 06:00:00', end: '2025-10-05 00:00:00', progress: 0, custom_class: 'bar-outline-red' },
                // 工作船3
                { id: 's3_j_guard', name: '工作船3-警戒  J船警戒', start: '2025-10-01 00:00:00', end: '2025-10-01 18:00:00', progress: 0, custom_class: 'bar-outline-red' },
                { id: 's3_k_guard', name: '工作船3-警戒  K船警戒', start: '2025-10-03 00:00:00', end: '2025-10-04 06:00:00', progress: 0, custom_class: 'bar-outline-red' },
                { id: 's3_l_guard', name: '工作船3-警戒  L船',     start: '2025-10-04 06:00:00', end: '2025-10-05 00:00:00', progress: 0, custom_class: 'bar-outline-red' },
            ]
            new Gantt(this.$refs.gantt1, tasks, {
                view_mode: 'Quarter Day',
                date_format: 'YYYY-MM-DD HH:mm:ss',
                show_expected_progress: false,
            })
        }
    }
}
</script>

<style lang="scss">
.frappe-wrapper {
    padding: 16px;

    h3 {
        margin-bottom: 8px;
        font-size: 15px;
        color: #2c3e50;
    }
}

.gantt-content {
    width: 100%;
    overflow-x: auto;
    overflow-y: hidden;

    ::v-deep .gantt-container {
        margin-bottom: -1px;
    }
}

/* 顏色樣式 */
::v-deep {
    .bar-blue         .bar { fill: #a8c8e8 !important; stroke: #4a90c4 !important; }
    .bar-orange       .bar { fill: #e8b87a !important; stroke: #c07030 !important; }
    .bar-pink         .bar { fill: #f0b0c8 !important; stroke: #c03070 !important; }
    .bar-red          .bar { fill: #c03030 !important; stroke: #900000 !important; }
    .bar-outline-blue .bar { fill: transparent !important; stroke: #4a90c4 !important; stroke-width: 2 !important; }
    .bar-outline-pink .bar { fill: transparent !important; stroke: #c03070 !important; stroke-width: 2 !important; }
    .bar-outline-navy .bar { fill: transparent !important; stroke: #2c4a7a !important; stroke-width: 2 !important; }
    .bar-outline-red  .bar { fill: transparent !important; stroke: #cc0000 !important; stroke-width: 2 !important; }
}

.limitation-note {
    margin-top: 20px;
    padding: 10px 14px;
    background: #fff8e8;
    border-left: 4px solid #f0a020;
    font-size: 13px;
    color: #666;
    line-height: 1.6;
}
</style>
