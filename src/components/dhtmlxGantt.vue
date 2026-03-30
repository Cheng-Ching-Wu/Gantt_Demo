<template>
    <div class="dhtmlx-gantt">
        <div ref="dhtmlxGantt" style="width: 100%;"></div>
    </div>
</template>

<script>
import { gantt } from 'dhtmlx-gantt'
import 'dhtmlx-gantt/codebase/dhtmlxgantt.css'

const SECTIONS = [
    { name: '碼頭',  prefix: 'w', ids: ['w1','w2','w3','w4','w5','w6','w7','w8','w9','w10'] },
    { name: '浮桶',  prefix: 'b', ids: ['b1','b2','b3','b4','b5'] },
    { name: '錨地',  prefix: 'a', ids: ['a1','a2','a3','a4','a5'] },
    { name: '油駁船', prefix: 't', ids: ['t1','t2','t3'] },
]

export default {
    name: 'DhtmlxGantt',
    data() {
        return {
            blockedRanges: [
                { start: new Date(2025, 8, 30, 0, 0), end: new Date(2025, 9, 1, 0, 0) }
            ]
        }
    },
    mounted() {
        // ── 欄位：單一欄位，左側由 overlay 顯示 section 名稱 ──
        gantt.config.columns = [
            { name: 'text', label: '泊位', width: 100, align: 'center' },
        ]

        gantt.config.scales = [
            { unit: 'day',  step: 1, format: '%m/%d' },
            {
                unit: 'hour', step: 6, height: 40,
                format: date => {
                    const pad = n => String(n).padStart(2, '0')
                    const h1  = pad(date.getHours())
                    const end = new Date(date.getTime() + 6 * 60 * 60 * 1000)
                    const h2  = pad(end.getHours())
                    return `<div style="line-height:1.2">${h1}:00<br>${h2}:00</div>`
                }
            }
        ]
        gantt.config.start_date  = new Date(2025, 8, 30, 0, 0)
        gantt.config.end_date    = new Date(2025, 9,  9, 24, 0)
        gantt.config.show_progress  = false
        gantt.config.readonly       = true
        gantt.config.column_width   = 60
        gantt.config.scale_height   = 70
        gantt.config.autosize       = 'y'

        // ── Task 樣式 ──
        gantt.templates.task_class = (_s, _e, task) =>
            (task.isEmptyRow) ? 'hidden-bar' : ''

        gantt.templates.task_text  = (_s, _e, task) => task.bar_label || ''
        gantt.templates.task_style = (_s, _e, task) => {
            if (task.isEmptyRow) return ''
            const bg     = task.color       || 'transparent'
            const border = task.borderColor || task.color || '#888'
            return `background:${bg};border:2px solid ${border};color:#333;`
        }

        // ── 封鎖時段遮罩 + 每天分隔線加粗 ──
        gantt.templates.timeline_cell_class = (_task, date) => {
            const classes = []
            const t = date.getTime()
            for (const r of this.blockedRanges) {
                if (t >= r.start.getTime() && t < r.end.getTime()) classes.push('blocked-period')
            }
            if (date.getHours() === 0) classes.push('day-start')
            return classes.join(' ')
        }

        // ── Section label overlay（模擬 rowspan）──
        gantt.attachEvent('onGanttRender', () => {
            document.querySelectorAll('.section-label-overlay').forEach(el => el.remove())
            const gridData = document.querySelector('.gantt_grid_data')
            if (!gridData) return

            SECTIONS.forEach(({ name, ids }) => {
                const firstIdx = gantt.getTaskIndex(ids[0])
                if (firstIdx === -1) return
                const top    = gantt.getRowTop(firstIdx)
                const height = gantt.config.row_height * ids.length

                const el = document.createElement('div')
                el.className = 'section-label-overlay'
                el.textContent = name
                Object.assign(el.style, {
                    position:       'absolute',
                    left:           '0',
                    top:            `${top}px`,
                    width:          '50px',
                    height:         `${height}px`,
                    display:        'flex',
                    alignItems:     'center',
                    justifyContent: 'center',
                    writingMode:    'vertical-rl',
                    fontWeight:     'bold',
                    fontSize:       '13px',
                    backgroundColor:'#e8f0f8',
                    borderRight:    '1px solid #b0c4de',
                    borderBottom:   '2px solid #7a9abf',
                    zIndex:         '10',
                    pointerEvents:  'none',
                    boxSizing:      'border-box',
                })
                gridData.appendChild(el)
            })
        })

        gantt.init(this.$refs.dhtmlxGantt)
        gantt.clearAll()

        const d = (m, day, h = 0) => new Date(2025, m - 1, day, h, 0)
        const empty = (id, text) => ({
            id, text, isEmptyRow: true,
            start_date: d(9, 30, 0), end_date: d(9, 30, 0)
        })

        gantt.parse({
            data: [
                // ════ 碼頭 ════
                { id: 'w1',  text: '1',  bar_label: 'A船 MFO 70(L)',          start_date: d(10,1,6),  end_date: d(10,2,0),  color: '#a8c8e8', borderColor: '#4a90c4' },
                { id: 'w2',  text: '2',  bar_label: 'B船 MFO 50(L)',          start_date: d(10,1,6),  end_date: d(10,2,0),  color: '#a8c8e8', borderColor: '#4a90c4' },
                { id: 'w3',  text: '3',  bar_label: 'C船 MFO 45(L)',          start_date: d(10,1,6),  end_date: d(10,2,0),  color: '#a8c8e8', borderColor: '#4a90c4' },
                { id: 'w4',  text: '4',  bar_label: 'D船 MFO 200(H) MGO 50', start_date: d(10,4,0),  end_date: d(10,6,0),  color: '#a8c8e8', borderColor: '#4a90c4' },
                { id: 'w5',  text: '5',  bar_label: 'E船 MFO 40(L)',          start_date: d(10,4,0),  end_date: d(10,5,0),  color: '#a8c8e8', borderColor: '#4a90c4' },
                { id: 'w6',  text: '6',  bar_label: 'F船 MFO 150(L)',         start_date: d(10,7,0),  end_date: d(10,9,24), color: '#a8c8e8', borderColor: '#4a90c4' },
                { id: 'w7',  text: '7',  bar_label: 'G船 MFO 55(L)',          start_date: d(10,8,0),  end_date: d(10,9,24), color: '#a8c8e8', borderColor: '#4a90c4' },
                empty('w8',  '8'),
                empty('w9',  '9'),
                empty('w10', '10'),

                // ════ 浮桶 ════
                { id: 'b1', text: '1', bar_label: 'H船 MFO 225(L) MGO 40', start_date: d(10,5,0), end_date: d(10,8,24), color: '#e8b87a', borderColor: '#c07030' },
                { id: 'b2', text: '2', bar_label: 'I船 MFO 125(L)',         start_date: d(10,4,0), end_date: d(10,6,24), color: '#e8b87a', borderColor: '#c07030' },
                empty('b3', '3'),
                empty('b4', '4'),
                empty('b5', '5'),

                // ════ 錨地 ════
                { id: 'a1', text: '1', bar_label: 'J船 MFO 2,000(H)', start_date: d(10,1,0), end_date: d(10,2,0),  color: '#f0b0c8', borderColor: '#c03070' },
                { id: 'a2', text: '2', bar_label: 'K船 MFO 3,000(H)', start_date: d(10,2,0), end_date: d(10,4,0),  color: 'transparent', borderColor: '#4a90c4' },
                { id: 'a3', text: '3', bar_label: 'L船 MFO 2,500(H)', start_date: d(10,2,0), end_date: d(10,5,0),  color: 'transparent', borderColor: '#4a90c4' },
                { id: 'a4', text: '4', bar_label: 'M船 MFO 2,700(H)', start_date: d(10,4,0), end_date: d(10,6,18), color: 'transparent', borderColor: '#c03070' },
                { id: 'a5', text: '5', bar_label: 'N船 MFO 2,000(H)', start_date: d(10,5,0), end_date: d(10,9,24), color: 'transparent', borderColor: '#4a90c4' },

                // ════ 油駁船 ════
                { id: 't1', text: '1', bar_label: '', start_date: d(10,1,0), end_date: d(10,1,12), color: '#c03030', borderColor: '#900000' },
                empty('t2', '2'),
                empty('t3', '3'),
            ],
            links: []
        })
    },
    beforeDestroy() {}
}
</script>

<style lang="scss">
.dhtmlx-gantt {
    width: 100%;
    height: fit-content;
    max-height: calc(100vh - 64px);
}

.hidden-bar {
    display: none !important;
}

/* 泊位數字向右偏移，避免被 section overlay 蓋住 */
.gantt_grid_data .gantt_row .gantt_cell {
    padding-left: 54px !important;
}

/* 每天起始格加粗左邊框 */
.gantt_task_cell.day-start {
    border-left: 1px solid #888 !important;
}

/* 封鎖時段：紅色框遮罩 */
.gantt_task_cell.blocked-period {
    background-color: rgba(200, 30, 30, 0.06);
    box-shadow: inset 0 0 0 0px #cc3333;
}
</style>
