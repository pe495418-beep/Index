<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Lumen — Tu Agenda Estudiantil</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Cormorant+Garamond:ital,wght@0,400;0,600;1,400&family=DM+Sans:wght@300;400;500&display=swap" rel="stylesheet">
<style>
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

  :root {
    --ink:    #1a1a2e;
    --accent: #e6a817;
    --gold2:  #f5c842;
    --cream:  #fdf6e3;
    --paper:  #fffdf5;
    --muted:  #9e9e7e;
    --border: #e8e0cc;
    --done:   #b5b5a0;
    --red:    #d95f5f;
    --green:  #5fad8e;
    --blue:   #5f88ad;
    --purple: #9b7fd4;
    --orange: #e07b39;
  }

  html { font-size: 15px; }
  body { font-family: 'DM Sans', sans-serif; background: var(--cream); color: var(--ink); min-height: 100vh; display: flex; flex-direction: column; }

  /* ══ HEADER ══════════════════════════════════════ */
  header {
    background: var(--ink); color: var(--cream);
    padding: 0 1.8rem; height: 58px;
    display: flex; align-items: center; justify-content: space-between;
    position: sticky; top: 0; z-index: 100;
    box-shadow: 0 2px 20px rgba(0,0,0,.35); flex-shrink: 0;
  }
  .header-left { display: flex; align-items: center; gap: .75rem; }
  .logo-icon {
    width: 32px; height: 32px; background: var(--accent);
    border-radius: 8px; display: flex; align-items: center; justify-content: center;
  }
  .logo { font-family: 'Cormorant Garamond', serif; font-size: 1.75rem; font-weight: 600; letter-spacing: .04em; color: var(--accent); }
  .logo span { color: var(--cream); font-style: italic; font-weight: 400; }
  #live-clock { display: flex; align-items: center; gap: .45rem; font-size: .82rem; color: #b0b0a0; letter-spacing: .04em; }

  /* ══ LAYOUT ══════════════════════════════════════ */
  main { display: grid; grid-template-columns: 310px 1fr; flex: 1; overflow: hidden; height: calc(100vh - 58px); }

  /* ══ SIDEBAR ══════════════════════════════════════ */
  aside { background: var(--paper); border-right: 1px solid var(--border); display: flex; flex-direction: column; overflow: hidden; }

  /* ── CALENDAR ── */
  .calendar-wrap { padding: 1.2rem 1.3rem .9rem; border-bottom: 1px solid var(--border); flex-shrink: 0; }
  .cal-header { display: flex; align-items: center; justify-content: space-between; margin-bottom: .85rem; }
  .cal-title {
    font-family: 'Cormorant Garamond', serif; font-size: 1.1rem; font-weight: 600;
    color: var(--ink); text-transform: capitalize; display: flex; align-items: center; gap: .4rem;
  }
  .cal-nav {
    background: none; border: 1px solid var(--border); border-radius: 7px;
    width: 27px; height: 27px; cursor: pointer; color: var(--ink);
    display: flex; align-items: center; justify-content: center; transition: background .15s, color .15s;
  }
  .cal-nav:hover { background: var(--ink); color: var(--cream); }
  .cal-grid { display: grid; grid-template-columns: repeat(7,1fr); gap: 2px; text-align: center; }
  .cal-day-name { font-size: .61rem; font-weight: 500; color: var(--muted); text-transform: uppercase; letter-spacing: .08em; padding: 3px 0; }
  .cal-cell {
    aspect-ratio: 1; display: flex; align-items: center; justify-content: center;
    border-radius: 50%; font-size: .77rem; cursor: pointer;
    transition: background .15s, color .15s; position: relative;
  }
  .cal-cell:hover { background: var(--border); }
  .cal-cell.other-month { color: var(--done); }
  .cal-cell.today { background: var(--accent); color: var(--ink); font-weight: 500; }
  .cal-cell.selected { background: var(--ink); color: var(--cream); }
  .cal-cell.has-event::after {
    content: ''; position: absolute; bottom: 3px;
    width: 4px; height: 4px; border-radius: 50%; background: var(--accent);
  }
  .cal-cell.today.has-event::after { background: var(--ink); }
  .cal-cell.has-exam::before {
    content: ''; position: absolute; top: 3px;
    width: 4px; height: 4px; border-radius: 50%; background: var(--red);
  }

  /* ── SIDEBAR TABS ── */
  .sidebar-tabs { display: flex; border-bottom: 1px solid var(--border); flex-shrink: 0; }
  .stab {
    flex: 1; display: flex; align-items: center; justify-content: center; gap: .3rem;
    padding: .6rem .3rem; font-size: .68rem; font-weight: 500; color: var(--muted);
    cursor: pointer; border-bottom: 2px solid transparent; transition: color .2s, border-color .2s; user-select: none;
  }
  .stab:hover { color: var(--ink); }
  .stab.active { color: var(--ink); border-bottom-color: var(--accent); }

  /* ── TASKS ── */
  .tasks-section { padding: .9rem 1.3rem; display: flex; flex-direction: column; overflow: hidden; flex: 1; }
  .tasks-section.hidden, .notes-side-section.hidden, .exams-side-section.hidden { display: none; }
  .section-title {
    font-family: 'Cormorant Garamond', serif; font-size: 1.05rem; font-weight: 600;
    margin-bottom: .75rem; display: flex; align-items: center; gap: .45rem;
  }
  .section-title .badge {
    background: var(--accent); color: var(--ink); font-family: 'DM Sans', sans-serif;
    font-size: .6rem; font-weight: 500; padding: 1px 7px; border-radius: 20px;
    min-width: 20px; text-align: center; margin-left: auto;
  }
  .add-task-form { display: flex; gap: .35rem; margin-bottom: .75rem; }
  .add-task-form input[type="text"] {
    flex: 1; border: 1px solid var(--border); border-radius: 8px; padding: .4rem .7rem;
    font-family: 'DM Sans', sans-serif; font-size: .79rem; background: var(--cream); color: var(--ink);
    outline: none; transition: border-color .2s;
  }
  .add-task-form input[type="text"]:focus { border-color: var(--accent); }
  .task-priority {
    border: 1px solid var(--border); border-radius: 8px; padding: .4rem .4rem;
    font-size: .78rem; background: var(--cream); color: var(--ink); outline: none; cursor: pointer;
  }
  .btn-icon {
    background: var(--ink); color: var(--cream); border: none; border-radius: 8px;
    padding: .4rem .7rem; cursor: pointer; display: flex; align-items: center; justify-content: center;
    transition: background .2s, color .2s; flex-shrink: 0;
  }
  .btn-icon:hover { background: var(--accent); color: var(--ink); }
  .task-list { list-style: none; overflow-y: auto; flex: 1; display: flex; flex-direction: column; gap: .38rem; padding-right: 3px; }
  .task-list::-webkit-scrollbar { width: 4px; }
  .task-list::-webkit-scrollbar-thumb { background: var(--border); border-radius: 4px; }
  .task-item {
    display: flex; align-items: flex-start; gap: .5rem;
    background: var(--cream); border: 1px solid var(--border); border-radius: 10px;
    padding: .48rem .65rem; animation: slideIn .25s ease; position: relative; overflow: hidden;
  }
  .task-item::before { content: ''; position: absolute; left: 0; top: 0; bottom: 0; width: 3px; border-radius: 10px 0 0 10px; }
  .task-item.p-alta::before  { background: var(--red); }
  .task-item.p-media::before { background: var(--accent); }
  .task-item.p-baja::before  { background: var(--green); }
  .task-check { width: 14px; height: 14px; min-width: 14px; margin-top: 2px; accent-color: var(--ink); cursor: pointer; }
  .task-text { flex: 1; font-size: .78rem; line-height: 1.4; transition: color .2s, text-decoration .2s; }
  .task-item.done .task-text { text-decoration: line-through; color: var(--done); }
  .task-del { background: none; border: none; color: var(--done); cursor: pointer; display: flex; align-items: center; padding: 0; transition: color .2s; }
  .task-del:hover { color: var(--red); }

  /* ── NOTES SIDE ── */
  .notes-side-section { padding: .9rem 1.3rem; display: flex; flex-direction: column; overflow: hidden; flex: 1; }
  .add-wide-btn {
    display: flex; align-items: center; justify-content: center; gap: .4rem; width: 100%;
    background: var(--ink); color: var(--cream); border: none; border-radius: 9px;
    padding: .45rem; font-family: 'DM Sans', sans-serif; font-size: .79rem; cursor: pointer;
    margin-bottom: .75rem; transition: background .2s, color .2s; flex-shrink: 0;
  }
  .add-wide-btn:hover { background: var(--accent); color: var(--ink); }
  .side-list { display: flex; flex-direction: column; gap: .38rem; overflow-y: auto; flex: 1; }
  .side-list::-webkit-scrollbar { width: 4px; }
  .side-list::-webkit-scrollbar-thumb { background: var(--border); border-radius: 4px; }
  .note-side-card {
    border-radius: 10px; padding: .6rem .75rem .6rem .8rem; border: 1px solid var(--border);
    cursor: pointer; transition: transform .15s, box-shadow .15s; animation: slideIn .25s ease; position: relative; overflow: hidden;
  }
  .note-side-card:hover { transform: translateX(3px); box-shadow: -3px 0 0 var(--accent); }
  .note-side-card.nc-0 { background: #fff4f4; }
  .note-side-card.nc-1 { background: #fffbea; }
  .note-side-card.nc-2 { background: #f0f5ff; }
  .note-side-card.nc-3 { background: #f0fff8; }
  .note-side-card.nc-4 { background: #f8f3ff; }
  .note-side-card.nc-5 { background: var(--cream); }
  .note-side-title { font-weight: 500; font-size: .79rem; color: var(--ink); white-space: nowrap; overflow: hidden; text-overflow: ellipsis; margin-bottom: .15rem; padding-right: 1.2rem; }
  .note-side-preview { font-size: .69rem; color: var(--muted); white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
  .side-del { position: absolute; top: .4rem; right: .45rem; background: none; border: none; color: var(--done); cursor: pointer; display: flex; padding: 2px; transition: color .2s; }
  .side-del:hover { color: var(--red); }

  /* ── EXAMS SIDE ── */
  .exams-side-section { padding: .9rem 1.3rem; display: flex; flex-direction: column; overflow: hidden; flex: 1; }
  .exam-side-card {
    border-radius: 10px; padding: .6rem .75rem .6rem .8rem; border: 1px solid var(--border);
    background: var(--cream); cursor: pointer; animation: slideIn .25s ease;
    position: relative; overflow: hidden; transition: transform .15s, box-shadow .15s;
  }
  .exam-side-card:hover { transform: translateX(3px); box-shadow: -3px 0 0 var(--red); }
  .exam-side-card::before { content: ''; position: absolute; left: 0; top: 0; bottom: 0; width: 3px; border-radius: 10px 0 0 10px; }
  .exam-side-card.urg-high::before  { background: var(--red); }
  .exam-side-card.urg-mid::before   { background: var(--orange); }
  .exam-side-card.urg-low::before   { background: var(--green); }
  .exam-side-card.urg-done::before  { background: var(--done); }
  .exam-side-subject { font-weight: 500; font-size: .79rem; color: var(--ink); padding-right: 1.2rem; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
  .exam-side-meta { font-size: .68rem; color: var(--muted); margin-top: .1rem; display: flex; gap: .5rem; flex-wrap: wrap; }
  .exam-side-countdown {
    font-size: .65rem; font-weight: 500; padding: 1px 7px; border-radius: 12px; margin-top: .25rem; display: inline-block;
  }
  .urg-high  .exam-side-countdown { background: rgba(217,95,95,.12);  color: var(--red); }
  .urg-mid   .exam-side-countdown { background: rgba(224,123,57,.12); color: var(--orange); }
  .urg-low   .exam-side-countdown { background: rgba(95,173,142,.12); color: var(--green); }
  .urg-done  .exam-side-countdown { background: rgba(0,0,0,.05);      color: var(--done); }

  /* ══ RIGHT PANEL ══════════════════════════════════════ */
  .right-panel { display: flex; flex-direction: column; overflow: hidden; }
  .right-tabs-bar {
    background: var(--ink); display: flex; align-items: center;
    padding: 0 1.8rem; gap: 0; flex-shrink: 0;
  }
  .rtab {
    display: flex; align-items: center; gap: .38rem; padding: .7rem .9rem;
    font-size: .75rem; font-weight: 500; color: rgba(255,255,255,.4);
    cursor: pointer; border-bottom: 2px solid transparent;
    transition: color .2s, border-color .2s; user-select: none; white-space: nowrap;
  }
  .rtab:hover { color: rgba(255,255,255,.76); }
  .rtab.active { color: var(--accent); border-bottom-color: var(--accent); }

  /* ── DATE BAR ── */
  .date-bar {
    background: #222240; color: var(--cream);
    padding: .6rem 1.8rem; display: flex; align-items: center; justify-content: space-between; flex-shrink: 0;
  }
  .date-label {
    font-family: 'Cormorant Garamond', serif; font-size: 1.1rem; font-weight: 600;
    text-transform: capitalize; display: flex; align-items: center; gap: .5rem;
  }
  .add-event-btn {
    display: flex; align-items: center; gap: .4rem;
    background: var(--accent); color: var(--ink); border: none; border-radius: 8px;
    padding: .36rem .85rem; font-family: 'DM Sans', sans-serif; font-size: .74rem; font-weight: 500;
    cursor: pointer; transition: background .2s;
  }
  .add-event-btn:hover { background: var(--gold2); }

  /* ── SCHEDULE ── */
  .schedule-panel { display: flex; flex-direction: column; flex: 1; overflow: hidden; }
  .schedule-panel.hidden, .notes-panel.hidden, .exams-panel.hidden { display: none; }
  .schedule-wrap { flex: 1; overflow-y: auto; padding: .8rem 1.8rem 2rem; }
  .schedule-wrap::-webkit-scrollbar { width: 5px; }
  .schedule-wrap::-webkit-scrollbar-thumb { background: var(--border); border-radius: 4px; }
  .hour-row { display: grid; grid-template-columns: 52px 1fr; min-height: 52px; border-bottom: 1px dashed var(--border); position: relative; }
  .hour-row:last-child { border-bottom: none; }
  .hour-label { font-size: .67rem; color: var(--muted); padding-top: 4px; font-weight: 300; user-select: none; }
  .hour-events { display: flex; flex-direction: column; gap: 4px; padding: 4px 0 4px 8px; }
  .event-chip {
    display: flex; align-items: center; gap: .5rem;
    border-radius: 8px; padding: .28rem .58rem; font-size: .75rem; animation: slideIn .2s ease;
  }
  .event-chip .ev-time { display: flex; align-items: center; gap: .25rem; opacity: .75; white-space: nowrap; }
  .event-chip.color-0 { background: rgba(214,95,95,.12);  color: #c04040; border-left: 3px solid var(--red); }
  .event-chip.color-1 { background: rgba(230,168,23,.12); color: #a07010; border-left: 3px solid var(--accent); }
  .event-chip.color-2 { background: rgba(95,137,173,.15); color: #3a6a96; border-left: 3px solid var(--blue); }
  .event-chip.color-3 { background: rgba(95,173,142,.12); color: #2e8060; border-left: 3px solid var(--green); }
  .event-chip.color-4 { background: rgba(155,127,212,.12); color: #6a4fb0; border-left: 3px solid var(--purple); }
  .event-chip .ev-del { margin-left: auto; background: none; border: none; color: inherit; opacity: .4; cursor: pointer; display: flex; align-items: center; padding: 0 2px; transition: opacity .15s; }
  .event-chip .ev-del:hover { opacity: 1; }
  .current-time-line { position: absolute; left: 52px; right: 0; height: 2px; background: var(--accent); pointer-events: none; z-index: 5; }
  .current-time-line::before { content: ''; position: absolute; left: -4px; top: -3px; width: 8px; height: 8px; border-radius: 50%; background: var(--accent); }

  /* ══ NOTES PANEL ══════════════════════════════════════ */
  .notes-panel { flex: 1; overflow: hidden; display: flex; flex-direction: column; }
  .notes-toolbar { padding: .72rem 1.8rem; background: #f5f0e8; border-bottom: 1px solid var(--border); display: flex; align-items: center; gap: .55rem; flex-shrink: 0; flex-wrap: wrap; }
  .notes-search {
    flex: 1; border: 1px solid var(--border); border-radius: 9px; padding: .4rem .8rem .4rem 2.2rem;
    font-family: 'DM Sans', sans-serif; font-size: .81rem; background: var(--paper); color: var(--ink);
    outline: none; transition: border-color .2s; min-width: 120px;
    background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='13' height='13' fill='none' stroke='%239e9e7e' stroke-width='2' viewBox='0 0 24 24'%3E%3Ccircle cx='11' cy='11' r='8'/%3E%3Cpath d='m21 21-4.35-4.35'/%3E%3C/svg%3E");
    background-repeat: no-repeat; background-position: .7rem center;
  }
  .notes-search:focus { border-color: var(--accent); }
  .filter-scroll { display: flex; gap: .3rem; overflow-x: auto; flex-wrap: nowrap; }
  .filter-scroll::-webkit-scrollbar { height: 0; }
  .filter-chip {
    font-size: .69rem; padding: 3px 10px; border-radius: 20px; border: 1px solid var(--border);
    background: var(--paper); cursor: pointer; white-space: nowrap; transition: background .15s, color .15s;
  }
  .filter-chip.active { background: var(--ink); color: var(--cream); border-color: var(--ink); }
  .btn-new { display: flex; align-items: center; gap: .38rem; background: var(--ink); color: var(--cream); border: none; border-radius: 9px; padding: .4rem .9rem; font-family: 'DM Sans', sans-serif; font-size: .78rem; font-weight: 500; cursor: pointer; transition: background .2s, color .2s; white-space: nowrap; flex-shrink: 0; }
  .btn-new:hover { background: var(--accent); color: var(--ink); }
  .notes-grid { padding: 1rem 1.8rem; display: grid; grid-template-columns: repeat(auto-fill, minmax(195px,1fr)); gap: .85rem; overflow-y: auto; flex: 1; align-content: start; }
  .notes-grid::-webkit-scrollbar { width: 5px; }
  .notes-grid::-webkit-scrollbar-thumb { background: var(--border); border-radius: 4px; }
  .note-card { border-radius: 14px; border: 1px solid var(--border); padding: .95rem 1rem .8rem; cursor: pointer; animation: popIn .22s ease; position: relative; display: flex; flex-direction: column; gap: .35rem; transition: transform .2s, box-shadow .2s; min-height: 120px; }
  .note-card:hover { transform: translateY(-3px); box-shadow: 0 8px 24px rgba(0,0,0,.1); }
  .note-card.nc-0{background:#fff4f4} .note-card.nc-1{background:#fffbea} .note-card.nc-2{background:#f0f5ff}
  .note-card.nc-3{background:#f0fff8} .note-card.nc-4{background:#f8f3ff} .note-card.nc-5{background:var(--cream)}
  .note-card-header { display: flex; align-items: flex-start; justify-content: space-between; gap: .5rem; }
  .note-card-title { font-family: 'Cormorant Garamond', serif; font-size: .98rem; font-weight: 600; color: var(--ink); line-height: 1.3; flex: 1; }
  .note-card-del { background: none; border: none; cursor: pointer; color: var(--done); display: flex; padding: 2px; transition: color .2s; }
  .note-card-del:hover { color: var(--red); }
  .note-card-body { font-size: .75rem; color: var(--muted); line-height: 1.55; display: -webkit-box; -webkit-line-clamp: 4; -webkit-box-orient: vertical; overflow: hidden; flex: 1; }
  .note-card-footer { display: flex; align-items: center; justify-content: space-between; margin-top: auto; padding-top: .38rem; border-top: 1px solid rgba(0,0,0,.06); }
  .note-card-tag { font-size: .62rem; font-weight: 500; padding: 2px 8px; border-radius: 20px; text-transform: uppercase; letter-spacing: .05em; }
  .nc-0 .note-card-tag { background: rgba(217,95,95,.12);   color: #c04040; }
  .nc-1 .note-card-tag { background: rgba(230,168,23,.15);  color: #a07010; }
  .nc-2 .note-card-tag { background: rgba(95,137,173,.12);  color: #3a6a96; }
  .nc-3 .note-card-tag { background: rgba(95,173,142,.12);  color: #2e8060; }
  .nc-4 .note-card-tag { background: rgba(155,127,212,.12); color: #6a4fb0; }
  .nc-5 .note-card-tag { background: rgba(26,26,46,.07);    color: var(--ink); }
  .note-card-date { font-size: .63rem; color: var(--done); }

  /* ══ EXAMS PANEL ══════════════════════════════════════ */
  .exams-panel { flex: 1; overflow: hidden; display: flex; flex-direction: column; }

  .exams-toolbar {
    padding: .72rem 1.8rem; background: #f5f0e8; border-bottom: 1px solid var(--border);
    display: flex; align-items: center; gap: .55rem; flex-shrink: 0;
  }
  .exams-toolbar-title {
    font-family: 'Cormorant Garamond', serif; font-size: 1.15rem; font-weight: 600; flex: 1;
    display: flex; align-items: center; gap: .5rem; color: var(--ink);
  }

  .exams-body { padding: 1.1rem 1.8rem; display: flex; flex-direction: column; gap: 1.2rem; overflow-y: auto; flex: 1; }
  .exams-body::-webkit-scrollbar { width: 5px; }
  .exams-body::-webkit-scrollbar-thumb { background: var(--border); border-radius: 4px; }

  /* Exam stats bar */
  .exam-stats {
    display: grid; grid-template-columns: repeat(3, 1fr); gap: .7rem;
  }
  .stat-card {
    background: var(--paper); border: 1px solid var(--border); border-radius: 12px;
    padding: .85rem 1rem; display: flex; flex-direction: column; gap: .3rem;
  }
  .stat-card .stat-num {
    font-family: 'Cormorant Garamond', serif; font-size: 2rem; font-weight: 600; line-height: 1;
  }
  .stat-card .stat-label { font-size: .7rem; color: var(--muted); text-transform: uppercase; letter-spacing: .06em; }
  .stat-card.urgent .stat-num { color: var(--red); }
  .stat-card.upcoming .stat-num { color: var(--orange); }
  .stat-card.all .stat-num { color: var(--ink); }

  /* Exam cards grid */
  .exams-grid {
    display: grid; grid-template-columns: repeat(auto-fill, minmax(280px,1fr)); gap: .85rem; align-content: start;
  }

  .exam-card {
    background: var(--paper); border: 1px solid var(--border); border-radius: 14px;
    padding: 1.1rem 1.1rem 1rem; position: relative; overflow: hidden;
    animation: popIn .22s ease; transition: transform .2s, box-shadow .2s;
    display: flex; flex-direction: column; gap: .55rem;
  }
  .exam-card:hover { transform: translateY(-2px); box-shadow: 0 8px 24px rgba(0,0,0,.1); }
  .exam-card::before { content: ''; position: absolute; left: 0; top: 0; bottom: 0; width: 4px; border-radius: 14px 0 0 14px; }
  .exam-card.urg-high::before  { background: var(--red); }
  .exam-card.urg-mid::before   { background: var(--orange); }
  .exam-card.urg-low::before   { background: var(--green); }
  .exam-card.urg-done::before  { background: var(--done); }

  .exam-card-top { display: flex; align-items: flex-start; justify-content: space-between; gap: .5rem; }

  .exam-subject {
    font-family: 'Cormorant Garamond', serif; font-size: 1.1rem; font-weight: 600; line-height: 1.3; flex: 1; color: var(--ink);
  }

  .exam-countdown {
    font-size: .66rem; font-weight: 500; padding: 3px 10px; border-radius: 20px; white-space: nowrap; flex-shrink: 0;
  }
  .urg-high  .exam-countdown { background: rgba(217,95,95,.12);  color: var(--red); }
  .urg-mid   .exam-countdown { background: rgba(224,123,57,.12); color: var(--orange); }
  .urg-low   .exam-countdown { background: rgba(95,173,142,.12); color: var(--green); }
  .urg-done  .exam-countdown { background: rgba(0,0,0,.06);      color: var(--done); }

  .exam-type-badge {
    font-size: .62rem; font-weight: 500; text-transform: uppercase; letter-spacing: .06em;
    padding: 2px 9px; border-radius: 20px; border: 1px solid var(--border);
    background: var(--cream); color: var(--muted); align-self: flex-start;
  }

  .exam-details { display: flex; flex-direction: column; gap: .32rem; }
  .exam-detail-row { display: flex; align-items: center; gap: .55rem; font-size: .78rem; color: var(--ink); }
  .exam-detail-row .detail-icon { color: var(--muted); display: flex; align-items: center; flex-shrink: 0; }
  .exam-detail-row .detail-val { flex: 1; }
  .exam-detail-row .detail-label { font-size: .67rem; color: var(--muted); text-transform: uppercase; letter-spacing: .05em; min-width: 50px; }

  .exam-notes-text { font-size: .74rem; color: var(--muted); line-height: 1.5; padding-top: .4rem; border-top: 1px solid var(--border); font-style: italic; }

  .exam-card-actions { display: flex; gap: .4rem; margin-top: auto; }
  .exam-action-btn {
    display: flex; align-items: center; gap: .3rem; border: 1px solid var(--border); border-radius: 8px;
    padding: .3rem .7rem; font-size: .73rem; cursor: pointer; background: none; color: var(--ink);
    transition: background .15s, color .15s;
  }
  .exam-action-btn:hover { background: var(--ink); color: var(--cream); border-color: var(--ink); }
  .exam-action-btn.danger { color: var(--red); }
  .exam-action-btn.danger:hover { background: var(--red); color: var(--cream); border-color: var(--red); }

  /* Sort/filter exams bar */
  .exams-filter { display: flex; gap: .35rem; overflow-x: auto; flex-wrap: nowrap; }
  .exams-filter::-webkit-scrollbar { height: 0; }

  /* Section header */
  .exams-section-header {
    display: flex; align-items: center; gap: .5rem;
    font-size: .7rem; font-weight: 500; color: var(--muted); text-transform: uppercase; letter-spacing: .08em;
    margin-bottom: .5rem;
  }
  .exams-section-header::after { content: ''; flex: 1; height: 1px; background: var(--border); }

  /* ══ MODALS ══════════════════════════════════════ */
  @keyframes slideIn { from { opacity:0; transform:translateY(-8px); } to { opacity:1; transform:translateY(0); } }
  @keyframes popIn   { from { opacity:0; transform:scale(.94); }       to { opacity:1; transform:scale(1); } }

  .modal-overlay {
    display: none; position: fixed; inset: 0;
    background: rgba(26,26,46,.55); z-index: 200; backdrop-filter: blur(4px);
    align-items: center; justify-content: center;
  }
  .modal-overlay.open { display: flex; }
  .modal {
    background: var(--paper); border-radius: 16px; padding: 1.55rem;
    width: min(420px, 95vw); box-shadow: 0 24px 60px rgba(0,0,0,.28); animation: popIn .25s ease;
    max-height: 90vh; overflow-y: auto;
  }
  .modal-header { display: flex; align-items: center; gap: .5rem; margin-bottom: 1rem; }
  .modal-header h3 { font-family: 'Cormorant Garamond', serif; font-size: 1.2rem; font-weight: 600; color: var(--ink); }
  .form-group { margin-bottom: .75rem; }
  .form-group label { display: block; font-size: .69rem; font-weight: 500; color: var(--muted); text-transform: uppercase; letter-spacing: .07em; margin-bottom: .25rem; }
  .form-group input, .form-group select, .form-group textarea {
    width: 100%; border: 1px solid var(--border); border-radius: 9px; padding: .46rem .72rem;
    font-family: 'DM Sans', sans-serif; font-size: .82rem; background: var(--cream); color: var(--ink); outline: none; transition: border-color .2s;
  }
  .form-group input:focus, .form-group select:focus, .form-group textarea:focus { border-color: var(--accent); }
  .form-group textarea { resize: vertical; min-height: 80px; line-height: 1.5; }
  .form-row { display: grid; grid-template-columns: 1fr 1fr; gap: .6rem; }
  .color-picker { display: flex; gap: .45rem; margin-top: .3rem; }
  .color-opt { width: 22px; height: 22px; border-radius: 50%; cursor: pointer; border: 2px solid transparent; transition: transform .15s, border-color .15s; }
  .color-opt:hover { transform: scale(1.15); }
  .color-opt.active { border-color: var(--ink); transform: scale(1.1); }
  .color-opt[data-c="0"]{background:var(--red)}   .color-opt[data-c="1"]{background:var(--accent)}
  .color-opt[data-c="2"]{background:var(--blue)}  .color-opt[data-c="3"]{background:var(--green)}
  .color-opt[data-c="4"]{background:var(--purple)} .color-opt[data-c="5"]{background:var(--ink)}
  .tag-chips { display: flex; gap: .35rem; flex-wrap: wrap; margin-top: .3rem; }
  .tag-chip { font-size: .69rem; padding: 3px 10px; border-radius: 20px; border: 1px solid var(--border); background: var(--cream); cursor: pointer; transition: background .15s, color .15s; }
  .tag-chip.selected { background: var(--ink); color: var(--cream); border-color: var(--ink); }
  .modal-actions { display: flex; gap: .5rem; margin-top: 1rem; justify-content: flex-end; }
  .btn-cancel { background: none; border: 1px solid var(--border); border-radius: 9px; padding: .43rem 1rem; font-family: 'DM Sans', sans-serif; font-size: .79rem; cursor: pointer; color: var(--ink); transition: background .15s; }
  .btn-cancel:hover { background: var(--border); }
  .btn-save { display: flex; align-items: center; gap: .35rem; background: var(--ink); color: var(--cream); border: none; border-radius: 9px; padding: .43rem 1.2rem; font-family: 'DM Sans', sans-serif; font-size: .79rem; cursor: pointer; transition: background .2s, color .2s; }
  .btn-save:hover { background: var(--accent); color: var(--ink); }

  /* Note view */
  .note-view-modal { background: var(--paper); border-radius: 16px; padding: 1.8rem; width: min(560px,95vw); max-height: 82vh; overflow-y: auto; box-shadow: 0 24px 60px rgba(0,0,0,.28); animation: popIn .25s ease; }
  .note-view-modal h2 { font-family: 'Cormorant Garamond', serif; font-size: 1.55rem; font-weight: 600; margin-bottom: .9rem; line-height: 1.3; }
  .note-view-body { font-size: .87rem; line-height: 1.75; color: var(--ink); white-space: pre-wrap; }
  .note-view-meta { margin-top: 1rem; padding-top: .7rem; border-top: 1px solid var(--border); display: flex; align-items: center; justify-content: space-between; font-size: .72rem; color: var(--muted); }
  .note-view-actions { display: flex; gap: .5rem; margin-top: 1rem; justify-content: flex-end; }

  /* Empty states */
  .empty-state { display: flex; flex-direction: column; align-items: center; justify-content: center; padding: 3rem 0; color: var(--muted); gap: .6rem; grid-column: 1/-1; }
  .empty-state svg { opacity: .22; }
  .empty-state p { font-size: .84rem; }
  .empty-day { display: flex; flex-direction: column; align-items: center; justify-content: center; padding: 3rem 0; color: var(--muted); gap: .5rem; }
  .empty-day svg { opacity: .28; }
  .empty-day p { font-size: .84rem; }
  .hour-anchor { scroll-margin-top: 20px; }
</style>
</head>
<body>

<!-- ══ HEADER ══ -->
<header>
  <div class="header-left">
    <div class="logo-icon">
      <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="#1a1a2e" stroke-width="2.5">
        <path d="M12 2L2 7l10 5 10-5-10-5z"/><path d="M2 17l10 5 10-5"/><path d="M2 12l10 5 10-5"/>
      </svg>
    </div>
    <div class="logo">Lumen <span>/ agenda</span></div>
  </div>
  <div id="live-clock">
    <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
      <circle cx="12" cy="12" r="10"/><polyline points="12 6 12 12 16 14"/>
    </svg>
    <span id="clock-text">--:--</span>
  </div>
</header>

<!-- ══ MAIN ══ -->
<main>

  <!-- ══ SIDEBAR ══ -->
  <aside>
    <!-- Calendar -->
    <div class="calendar-wrap">
      <div class="cal-header">
        <button class="cal-nav" id="prev-month">
          <svg width="13" height="13" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><polyline points="15 18 9 12 15 6"/></svg>
        </button>
        <div class="cal-title">
          <svg width="13" height="13" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
            <rect x="3" y="4" width="18" height="18" rx="2"/><line x1="16" y1="2" x2="16" y2="6"/>
            <line x1="8" y1="2" x2="8" y2="6"/><line x1="3" y1="10" x2="21" y2="10"/>
          </svg>
          <span id="cal-title"></span>
        </div>
        <button class="cal-nav" id="next-month">
          <svg width="13" height="13" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><polyline points="9 18 15 12 9 6"/></svg>
        </button>
      </div>
      <div class="cal-grid" id="cal-grid"></div>
    </div>

    <!-- Sidebar tabs -->
    <div class="sidebar-tabs">
      <div class="stab active" data-stab="tasks">
        <svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
          <path d="M9 11l3 3L22 4"/><path d="M21 12v7a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h11"/>
        </svg>
        Tareas
      </div>
      <div class="stab" data-stab="exams-side">
        <svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
          <path d="M22 10v6M2 10l10-5 10 5-10 5z"/><path d="M6 12v5c3 3 9 3 12 0v-5"/>
        </svg>
        Exámenes
      </div>
      <div class="stab" data-stab="notes-side">
        <svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
          <path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"/>
          <polyline points="14 2 14 8 20 8"/><line x1="16" y1="13" x2="8" y2="13"/><line x1="16" y1="17" x2="8" y2="17"/>
        </svg>
        Notas
      </div>
    </div>

    <!-- Tasks panel -->
    <div class="tasks-section" id="stab-tasks">
      <div class="section-title">
        <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
          <rect x="3" y="3" width="18" height="18" rx="3"/><path d="M9 12l2 2 4-4"/>
        </svg>
        Pendientes
        <span class="badge" id="task-count">0</span>
      </div>
      <div class="add-task-form">
        <input type="text" id="task-input" placeholder="Nueva tarea..." maxlength="80">
        <select class="task-priority" id="task-priority">
          <option value="p-alta">🔴</option>
          <option value="p-media" selected>🟡</option>
          <option value="p-baja">🟢</option>
        </select>
        <button class="btn-icon" id="btn-add-task">
          <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5">
            <line x1="12" y1="5" x2="12" y2="19"/><line x1="5" y1="12" x2="19" y2="12"/>
          </svg>
        </button>
      </div>
      <ul class="task-list" id="task-list"></ul>
    </div>

    <!-- Exams side panel -->
    <div class="exams-side-section hidden" id="stab-exams-side">
      <button class="add-wide-btn" id="open-exam-modal-side">
        <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5">
          <line x1="12" y1="5" x2="12" y2="19"/><line x1="5" y1="12" x2="19" y2="12"/>
        </svg>
        Agregar examen
      </button>
      <div class="side-list" id="exams-side-list"></div>
    </div>

    <!-- Notes side panel -->
    <div class="notes-side-section hidden" id="stab-notes-side">
      <button class="add-wide-btn" id="open-note-modal-side">
        <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5">
          <line x1="12" y1="5" x2="12" y2="19"/><line x1="5" y1="12" x2="19" y2="12"/>
        </svg>
        Nueva nota
      </button>
      <div class="side-list" id="notes-side-list"></div>
    </div>
  </aside>

  <!-- ══ RIGHT PANEL ══ -->
  <div class="right-panel">

    <!-- Right tabs -->
    <div class="right-tabs-bar">
      <div class="rtab active" data-rtab="schedule">
        <svg width="13" height="13" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
          <rect x="3" y="4" width="18" height="18" rx="2"/><line x1="16" y1="2" x2="16" y2="6"/>
          <line x1="8" y1="2" x2="8" y2="6"/><line x1="3" y1="10" x2="21" y2="10"/>
        </svg>
        Horario
      </div>
      <div class="rtab" data-rtab="exams">
        <svg width="13" height="13" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
          <path d="M22 10v6M2 10l10-5 10 5-10 5z"/><path d="M6 12v5c3 3 9 3 12 0v-5"/>
        </svg>
        Exámenes
      </div>
      <div class="rtab" data-rtab="notes">
        <svg width="13" height="13" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
          <path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"/>
          <polyline points="14 2 14 8 20 8"/>
          <line x1="16" y1="13" x2="8" y2="13"/><line x1="16" y1="17" x2="8" y2="17"/>
        </svg>
        Notas
      </div>
    </div>

    <!-- ── SCHEDULE PANEL ── -->
    <div class="schedule-panel" id="panel-schedule">
      <div class="date-bar">
        <div class="date-label">
          <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
            <circle cx="12" cy="12" r="10"/><polyline points="12 6 12 12 16 14"/>
          </svg>
          <span id="selected-date-label">Selecciona un día</span>
        </div>
        <button class="add-event-btn" id="open-modal-btn">
          <svg width="13" height="13" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5">
            <line x1="12" y1="5" x2="12" y2="19"/><line x1="5" y1="12" x2="19" y2="12"/>
          </svg>
          Agregar evento
        </button>
      </div>
      <div class="schedule-wrap" id="schedule-wrap"></div>
    </div>

    <!-- ── EXAMS PANEL ── -->
    <div class="exams-panel hidden" id="panel-exams">
      <div class="exams-toolbar">
        <div class="exams-toolbar-title">
          <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="var(--red)" stroke-width="2">
            <path d="M22 10v6M2 10l10-5 10 5-10 5z"/><path d="M6 12v5c3 3 9 3 12 0v-5"/>
          </svg>
          Próximos Exámenes
        </div>
        <div class="exams-filter" id="exams-filter">
          <span class="filter-chip active" data-efilter="all">Todos</span>
          <span class="filter-chip" data-efilter="upcoming">Próximos</span>
          <span class="filter-chip" data-efilter="week">Esta semana</span>
          <span class="filter-chip" data-efilter="passed">Pasados</span>
        </div>
        <button class="btn-new" id="open-exam-modal-main">
          <svg width="13" height="13" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5">
            <line x1="12" y1="5" x2="12" y2="19"/><line x1="5" y1="12" x2="19" y2="12"/>
          </svg>
          Nuevo examen
        </button>
      </div>
      <div class="exams-body" id="exams-body"></div>
    </div>

    <!-- ── NOTES PANEL ── -->
    <div class="notes-panel hidden" id="panel-notes">
      <div class="notes-toolbar">
        <input type="text" class="notes-search" id="notes-search" placeholder="Buscar notas...">
        <div class="filter-scroll" id="notes-filter-bar">
          <span class="filter-chip active" data-filter="">Todas</span>
          <span class="filter-chip" data-filter="General">📚 General</span>
          <span class="filter-chip" data-filter="Matemáticas">📐 Mat.</span>
          <span class="filter-chip" data-filter="Ciencias">🔬 Ciencias</span>
          <span class="filter-chip" data-filter="Historia">🏛️ Historia</span>
          <span class="filter-chip" data-filter="Literatura">📖 Lit.</span>
          <span class="filter-chip" data-filter="Idea">💡 Idea</span>
        </div>
        <button class="btn-new" id="open-note-modal-main">
          <svg width="13" height="13" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5">
            <line x1="12" y1="5" x2="12" y2="19"/><line x1="5" y1="12" x2="19" y2="12"/>
          </svg>
          Nueva nota
        </button>
      </div>
      <div class="notes-grid" id="notes-grid"></div>
    </div>

  </div>
</main>

<!-- ══ EVENT MODAL ══ -->
<div class="modal-overlay" id="modal-overlay">
  <div class="modal">
    <div class="modal-header">
      <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="var(--accent)" stroke-width="2">
        <rect x="3" y="4" width="18" height="18" rx="2"/><line x1="16" y1="2" x2="16" y2="6"/>
        <line x1="8" y1="2" x2="8" y2="6"/><line x1="3" y1="10" x2="21" y2="10"/>
      </svg>
      <h3>Nuevo evento</h3>
    </div>
    <div class="form-group">
      <label>Título del evento</label>
      <input type="text" id="ev-title" placeholder="Ej: Clase de Cálculo" maxlength="60">
    </div>
    <div class="form-row">
      <div class="form-group">
        <label>Hora</label>
        <input type="time" id="ev-time" value="08:00">
      </div>
      <div class="form-group">
        <label>Color</label>
        <div class="color-picker" id="color-picker">
          <div class="color-opt active" data-c="0"></div>
          <div class="color-opt" data-c="1"></div>
          <div class="color-opt" data-c="2"></div>
          <div class="color-opt" data-c="3"></div>
          <div class="color-opt" data-c="4"></div>
        </div>
      </div>
    </div>
    <div class="modal-actions">
      <button class="btn-cancel" id="close-modal-btn">Cancelar</button>
      <button class="btn-save" id="save-event-btn">
        <svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
          <path d="M19 21H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h11l5 5v11a2 2 0 0 1-2 2z"/>
          <polyline points="17 21 17 13 7 13 7 21"/><polyline points="7 3 7 8 15 8"/>
        </svg>
        Guardar
      </button>
    </div>
  </div>
</div>

<!-- ══ EXAM MODAL ══ -->
<div class="modal-overlay" id="exam-modal-overlay">
  <div class="modal">
    <div class="modal-header">
      <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="var(--red)" stroke-width="2">
        <path d="M22 10v6M2 10l10-5 10 5-10 5z"/><path d="M6 12v5c3 3 9 3 12 0v-5"/>
      </svg>
      <h3 id="exam-modal-title">Nuevo examen</h3>
    </div>

    <div class="form-group">
      <label>
        <svg width="10" height="10" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" style="vertical-align:middle;margin-right:3px">
          <path d="M2 3h6a4 4 0 0 1 4 4v14a3 3 0 0 0-3-3H2z"/><path d="M22 3h-6a4 4 0 0 0-4 4v14a3 3 0 0 1 3-3h7z"/>
        </svg>
        Materia / Asignatura
      </label>
      <input type="text" id="exam-subject" placeholder="Ej: Cálculo Diferencial" maxlength="60">
    </div>

    <div class="form-row">
      <div class="form-group">
        <label>
          <svg width="10" height="10" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" style="vertical-align:middle;margin-right:3px">
            <rect x="3" y="4" width="18" height="18" rx="2"/><line x1="16" y1="2" x2="16" y2="6"/>
            <line x1="8" y1="2" x2="8" y2="6"/><line x1="3" y1="10" x2="21" y2="10"/>
          </svg>
          Fecha del examen
        </label>
        <input type="date" id="exam-date">
      </div>
      <div class="form-group">
        <label>
          <svg width="10" height="10" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" style="vertical-align:middle;margin-right:3px">
            <circle cx="12" cy="12" r="10"/><polyline points="12 6 12 12 16 14"/>
          </svg>
          Hora
        </label>
        <input type="time" id="exam-time" value="08:00">
      </div>
    </div>

    <div class="form-row">
      <div class="form-group">
        <label>
          <svg width="10" height="10" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" style="vertical-align:middle;margin-right:3px">
            <path d="M3 9l9-7 9 7v11a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2z"/><polyline points="9 22 9 12 15 12 15 22"/>
          </svg>
          Aula / Salón
        </label>
        <input type="text" id="exam-room" placeholder="Ej: Aula 203 — Edificio A" maxlength="60">
      </div>
      <div class="form-group">
        <label>
          <svg width="10" height="10" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" style="vertical-align:middle;margin-right:3px">
            <path d="M20 21v-2a4 4 0 0 0-4-4H8a4 4 0 0 0-4 4v2"/><circle cx="12" cy="7" r="4"/>
          </svg>
          Nombre del maestro/a
        </label>
        <input type="text" id="exam-teacher" placeholder="Ej: Dr. García López" maxlength="60">
      </div>
    </div>

    <div class="form-group">
      <label>Tipo de examen</label>
      <select id="exam-type">
        <option value="Parcial">📋 Parcial</option>
        <option value="Final">🎓 Final</option>
        <option value="Quiz">⚡ Quiz / Evaluación rápida</option>
        <option value="Oral">🎤 Oral</option>
        <option value="Práctica">🔬 Práctica / Laboratorio</option>
        <option value="Proyecto">📁 Entrega de proyecto</option>
        <option value="Recuperación">🔄 Recuperación / Extraordinario</option>
      </select>
    </div>

    <div class="form-group">
      <label>Notas / Temas a estudiar</label>
      <textarea id="exam-notes" placeholder="Ej: Capítulos 3-5, derivadas implícitas, regla de la cadena..." rows="3"></textarea>
    </div>

    <div class="modal-actions">
      <button class="btn-cancel" id="close-exam-modal-btn">Cancelar</button>
      <button class="btn-save" id="save-exam-btn">
        <svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
          <path d="M19 21H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h11l5 5v11a2 2 0 0 1-2 2z"/>
          <polyline points="17 21 17 13 7 13 7 21"/>
        </svg>
        Guardar examen
      </button>
    </div>
  </div>
</div>

<!-- ══ NOTE MODAL ══ -->
<div class="modal-overlay" id="note-modal-overlay">
  <div class="modal">
    <div class="modal-header">
      <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="var(--accent)" stroke-width="2">
        <path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"/>
        <polyline points="14 2 14 8 20 8"/>
      </svg>
      <h3 id="note-modal-title">Nueva nota</h3>
    </div>
    <div class="form-group">
      <label>Título</label>
      <input type="text" id="note-title-input" placeholder="Título de la nota..." maxlength="80">
    </div>
    <div class="form-group">
      <label>Contenido</label>
      <textarea id="note-body-input" placeholder="Escribe aquí tus apuntes..." rows="5"></textarea>
    </div>
    <div class="form-group">
      <label>Materia / Etiqueta</label>
      <div class="tag-chips" id="note-tags-input">
        <span class="tag-chip selected" data-tag="General">📚 General</span>
        <span class="tag-chip" data-tag="Matemáticas">📐 Matemáticas</span>
        <span class="tag-chip" data-tag="Ciencias">🔬 Ciencias</span>
        <span class="tag-chip" data-tag="Historia">🏛️ Historia</span>
        <span class="tag-chip" data-tag="Literatura">📖 Literatura</span>
        <span class="tag-chip" data-tag="Idea">💡 Idea</span>
      </div>
    </div>
    <div class="form-group">
      <label>Color</label>
      <div class="color-picker" id="note-color-picker">
        <div class="color-opt active" data-c="5"></div>
        <div class="color-opt" data-c="0"></div>
        <div class="color-opt" data-c="1"></div>
        <div class="color-opt" data-c="2"></div>
        <div class="color-opt" data-c="3"></div>
        <div class="color-opt" data-c="4"></div>
      </div>
    </div>
    <div class="modal-actions">
      <button class="btn-cancel" id="close-note-modal-btn">Cancelar</button>
      <button class="btn-save" id="save-note-btn">
        <svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
          <path d="M19 21H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h11l5 5v11a2 2 0 0 1-2 2z"/>
          <polyline points="17 21 17 13 7 13 7 21"/>
        </svg>
        Guardar nota
      </button>
    </div>
  </div>
</div>

<!-- ══ NOTE VIEW MODAL ══ -->
<div class="modal-overlay" id="note-view-overlay">
  <div class="note-view-modal">
    <h2 id="nv-title"></h2>
    <div class="note-view-body" id="nv-body"></div>
    <div class="note-view-meta"><span id="nv-tag"></span><span id="nv-date"></span></div>
    <div class="note-view-actions">
      <button class="btn-cancel" id="close-note-view-btn">Cerrar</button>
      <button class="btn-save" id="edit-note-btn">
        <svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
          <path d="M11 4H4a2 2 0 0 0-2 2v14a2 2 0 0 0 2 2h14a2 2 0 0 0 2-2v-7"/>
          <path d="M18.5 2.5a2.121 2.121 0 0 1 3 3L12 15l-4 1 1-4 9.5-9.5z"/>
        </svg>
        Editar
      </button>
    </div>
  </div>
</div>

<script>
// ══ SVG ICON HELPERS ══════════════════════════════════════
const ICO = {
  trash:`<svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><polyline points="3 6 5 6 21 6"/><path d="M19 6l-1 14a2 2 0 0 1-2 2H8a2 2 0 0 1-2-2L5 6"/><path d="M10 11v6"/><path d="M14 11v6"/><path d="M9 6V4h6v2"/></svg>`,
  clock:`<svg width="11" height="11" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><circle cx="12" cy="12" r="10"/><polyline points="12 6 12 12 16 14"/></svg>`,
  edit:`<svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M11 4H4a2 2 0 0 0-2 2v14a2 2 0 0 0 2 2h14a2 2 0 0 0 2-2v-7"/><path d="M18.5 2.5a2.121 2.121 0 0 1 3 3L12 15l-4 1 1-4 9.5-9.5z"/></svg>`,
  map:`<svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M3 9l9-7 9 7v11a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2z"/><polyline points="9 22 9 12 15 12 15 22"/></svg>`,
  user:`<svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M20 21v-2a4 4 0 0 0-4-4H8a4 4 0 0 0-4 4v2"/><circle cx="12" cy="7" r="4"/></svg>`,
  calendar:`<svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><rect x="3" y="4" width="18" height="18" rx="2"/><line x1="16" y1="2" x2="16" y2="6"/><line x1="8" y1="2" x2="8" y2="6"/><line x1="3" y1="10" x2="21" y2="10"/></svg>`,
  book:`<svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M2 3h6a4 4 0 0 1 4 4v14a3 3 0 0 0-3-3H2z"/><path d="M22 3h-6a4 4 0 0 0-4 4v14a3 3 0 0 1 3-3h7z"/></svg>`,
  alert:`<svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M10.29 3.86L1.82 18a2 2 0 0 0 1.71 3h16.94a2 2 0 0 0 1.71-3L13.71 3.86a2 2 0 0 0-3.42 0z"/><line x1="12" y1="9" x2="12" y2="13"/><line x1="12" y1="17" x2="12.01" y2="17"/></svg>`,
};

// ══ STATE ══════════════════════════════════════
let viewYear, viewMonth, selectedDate=null;
let selectedEventColor=0, selectedNoteColor=5, selectedNoteTag='General';
let editingNoteId=null, viewingNoteId=null, editingExamId=null;
let activeNoteFilter='', noteSearchQ='', activeExamFilter='all';

let events = JSON.parse(localStorage.getItem('lumen_events')||'{}');
let tasks  = JSON.parse(localStorage.getItem('lumen_tasks') ||'[]');
let notes  = JSON.parse(localStorage.getItem('lumen_notes') ||'[]');
let exams  = JSON.parse(localStorage.getItem('lumen_exams') ||'[]');

function save() {
  localStorage.setItem('lumen_events', JSON.stringify(events));
  localStorage.setItem('lumen_tasks',  JSON.stringify(tasks));
  localStorage.setItem('lumen_notes',  JSON.stringify(notes));
  localStorage.setItem('lumen_exams',  JSON.stringify(exams));
}

// ══ CLOCK ══════════════════════════════════════
function updateClock() {
  const now=new Date(), h=now.getHours().toString().padStart(2,'0'), m=now.getMinutes().toString().padStart(2,'0');
  document.getElementById('clock-text').textContent=`${h}:${m} · ${now.toLocaleDateString('es-MX',{weekday:'short',day:'numeric',month:'short'})}`;
}
setInterval(updateClock,1000); updateClock();

// ══ TABS ══════════════════════════════════════
document.querySelectorAll('.stab').forEach(t=>t.addEventListener('click',()=>{
  document.querySelectorAll('.stab').forEach(x=>x.classList.remove('active')); t.classList.add('active');
  const id=t.dataset.stab;
  document.getElementById('stab-tasks').classList.toggle('hidden', id!=='tasks');
  document.getElementById('stab-exams-side').classList.toggle('hidden', id!=='exams-side');
  document.getElementById('stab-notes-side').classList.toggle('hidden', id!=='notes-side');
}));

document.querySelectorAll('.rtab').forEach(t=>t.addEventListener('click',()=>{
  document.querySelectorAll('.rtab').forEach(x=>x.classList.remove('active')); t.classList.add('active');
  const id=t.dataset.rtab;
  document.getElementById('panel-schedule').classList.toggle('hidden', id!=='schedule');
  document.getElementById('panel-exams').classList.toggle('hidden', id!=='exams');
  document.getElementById('panel-notes').classList.toggle('hidden', id!=='notes');
}));

// ══ CALENDAR ══════════════════════════════════════
const MONTHS=['Enero','Febrero','Marzo','Abril','Mayo','Junio','Julio','Agosto','Septiembre','Octubre','Noviembre','Diciembre'];
const DAYS=['Do','Lu','Ma','Mi','Ju','Vi','Sá'];

function toKey(d){return`${d.getFullYear()}-${String(d.getMonth()+1).padStart(2,'0')}-${String(d.getDate()).padStart(2,'0')}`}

function initCalendar(){
  const now=new Date(); viewYear=now.getFullYear(); viewMonth=now.getMonth();
  selectedDate=toKey(now); renderCalendar(); renderSchedule();
}

function renderCalendar(){
  document.getElementById('cal-title').textContent=`${MONTHS[viewMonth]} ${viewYear}`;
  const grid=document.getElementById('cal-grid'); grid.innerHTML='';
  DAYS.forEach(d=>{const el=document.createElement('div');el.className='cal-day-name';el.textContent=d;grid.appendChild(el);});

  const firstDay=new Date(viewYear,viewMonth,1).getDay();
  const daysInMonth=new Date(viewYear,viewMonth+1,0).getDate();
  const daysInPrev=new Date(viewYear,viewMonth,0).getDate();

  for(let i=firstDay-1;i>=0;i--) grid.appendChild(makeCell(daysInPrev-i,viewYear,viewMonth-1,true));
  for(let d=1;d<=daysInMonth;d++) grid.appendChild(makeCell(d,viewYear,viewMonth,false));
  const rem=(firstDay+daysInMonth)%7===0?0:7-((firstDay+daysInMonth)%7);
  for(let d=1;d<=rem;d++) grid.appendChild(makeCell(d,viewYear,viewMonth+1,true));
}

function makeCell(day,year,month,other){
  const date=new Date(year,month,day), key=toKey(date), todayK=toKey(new Date());
  const cell=document.createElement('div'); cell.className='cal-cell'; cell.textContent=day;
  if(other) cell.classList.add('other-month');
  if(key===todayK) cell.classList.add('today');
  if(key===selectedDate) cell.classList.add('selected');
  if(events[key]?.length) cell.classList.add('has-event');
  if(exams.some(e=>e.date===key)) cell.classList.add('has-exam');
  cell.addEventListener('click',()=>{
    selectedDate=key;
    if(other){viewYear=date.getFullYear();viewMonth=date.getMonth();}
    renderCalendar(); renderSchedule();
  });
  return cell;
}

document.getElementById('prev-month').addEventListener('click',()=>{viewMonth--;if(viewMonth<0){viewMonth=11;viewYear--;}renderCalendar();});
document.getElementById('next-month').addEventListener('click',()=>{viewMonth++;if(viewMonth>11){viewMonth=0;viewYear++;}renderCalendar();});

// ══ SCHEDULE ══════════════════════════════════════
const HOURS=Array.from({length:16},(_,i)=>i+6);

function renderSchedule(){
  if(!selectedDate)return;
  const wrap=document.getElementById('schedule-wrap'); wrap.innerHTML='';
  const [y,m,d]=selectedDate.split('-').map(Number), dateObj=new Date(y,m-1,d);
  document.getElementById('selected-date-label').textContent=
    dateObj.toLocaleDateString('es-MX',{weekday:'long',day:'numeric',month:'long',year:'numeric'});

  const dayEvents=events[selectedDate]||[], now=new Date(), isToday=selectedDate===toKey(now);
  let timeFrac=null;
  if(isToday){const s=HOURS[0],e=HOURS[HOURS.length-1]+1,f=(now.getHours()+now.getMinutes()/60-s)/(e-s);if(f>=0&&f<=1)timeFrac=f;}

  const outer=document.createElement('div'); outer.style.position='relative';
  HOURS.forEach(hour=>{
    const row=document.createElement('div'); row.className='hour-row'; row.id=`hour-${hour}`;
    if(isToday&&hour===now.getHours())row.classList.add('hour-anchor');
    const lbl=document.createElement('div'); lbl.className='hour-label'; lbl.textContent=`${String(hour).padStart(2,'0')}:00`;
    const evWrap=document.createElement('div'); evWrap.className='hour-events';
    dayEvents.filter(e=>parseInt(e.time.split(':')[0])===hour).forEach(ev=>{
      const chip=document.createElement('div'); chip.className=`event-chip color-${ev.color}`;
      chip.innerHTML=`<span class="ev-time">${ICO.clock}${ev.time}</span><strong>${esc(ev.title)}</strong>
        <button class="ev-del" data-id="${ev.id}">${ICO.trash}</button>`;
      evWrap.appendChild(chip);
    });
    row.appendChild(lbl); row.appendChild(evWrap); outer.appendChild(row);
  });

  // Exam indicators on schedule
  const dayExams=exams.filter(e=>e.date===selectedDate);
  if(dayExams.length){
    const examBanner=document.createElement('div');
    examBanner.style.cssText='background:rgba(217,95,95,.08);border:1px solid rgba(217,95,95,.2);border-radius:10px;padding:.6rem .9rem;margin-bottom:.6rem;display:flex;align-items:center;gap:.6rem;font-size:.78rem;color:#c04040;';
    examBanner.innerHTML=`${ICO.alert}<strong>Examen hoy:</strong> ${dayExams.map(e=>esc(e.subject)).join(', ')}`;
    wrap.appendChild(examBanner);
  }

  if(timeFrac!==null){const line=document.createElement('div');line.className='current-time-line';line.style.top=(timeFrac*HOURS.length*52)+'px';outer.appendChild(line);}
  wrap.appendChild(outer);

  if(!dayEvents.length&&!dayExams.length){
    const em=document.createElement('div');em.className='empty-day';
    em.innerHTML=`<svg width="48" height="48" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1"><rect x="3" y="4" width="18" height="18" rx="2"/><line x1="16" y1="2" x2="16" y2="6"/><line x1="8" y1="2" x2="8" y2="6"/><line x1="3" y1="10" x2="21" y2="10"/></svg><p>Sin eventos para este día</p>`;
    wrap.appendChild(em);
  }

  if(isToday)setTimeout(()=>document.getElementById(`hour-${now.getHours()}`)?.scrollIntoView({behavior:'smooth',block:'start'}),80);
  wrap.querySelectorAll('.ev-del').forEach(btn=>btn.addEventListener('click',()=>{
    events[selectedDate]=events[selectedDate].filter(ev=>ev.id!==btn.dataset.id);
    if(!events[selectedDate].length)delete events[selectedDate];
    save(); renderCalendar(); renderSchedule();
  }));
}

// ══ EVENT MODAL ══════════════════════════════════════
document.getElementById('open-modal-btn').addEventListener('click',()=>{document.getElementById('modal-overlay').classList.add('open');document.getElementById('ev-title').focus();});
document.getElementById('close-modal-btn').addEventListener('click',closeEventModal);
document.getElementById('modal-overlay').addEventListener('click',e=>{if(e.target===e.currentTarget)closeEventModal();});
function closeEventModal(){document.getElementById('modal-overlay').classList.remove('open');document.getElementById('ev-title').value='';}

document.querySelectorAll('#color-picker .color-opt').forEach(opt=>opt.addEventListener('click',()=>{
  document.querySelectorAll('#color-picker .color-opt').forEach(o=>o.classList.remove('active'));
  opt.classList.add('active'); selectedEventColor=parseInt(opt.dataset.c);
}));

document.getElementById('save-event-btn').addEventListener('click',()=>{
  const title=document.getElementById('ev-title').value.trim(), time=document.getElementById('ev-time').value;
  if(!title||!selectedDate)return;
  if(!events[selectedDate])events[selectedDate]=[];
  events[selectedDate].push({id:Date.now().toString(),title,time,color:selectedEventColor});
  events[selectedDate].sort((a,b)=>a.time.localeCompare(b.time));
  save(); closeEventModal(); renderCalendar(); renderSchedule();
});
document.getElementById('ev-title').addEventListener('keydown',e=>{if(e.key==='Enter')document.getElementById('save-event-btn').click();});

// ══ TASKS ══════════════════════════════════════
function renderTasks(){
  const list=document.getElementById('task-list'); list.innerHTML='';
  document.getElementById('task-count').textContent=tasks.filter(t=>!t.done).length;
  tasks.forEach((task,idx)=>{
    const li=document.createElement('li'); li.className=`task-item ${task.priority}${task.done?' done':''}`;
    const chk=document.createElement('input'); chk.type='checkbox'; chk.className='task-check'; chk.checked=task.done;
    chk.addEventListener('change',()=>{tasks[idx].done=chk.checked;save();renderTasks();});
    const span=document.createElement('span'); span.className='task-text'; span.textContent=task.text;
    const del=document.createElement('button'); del.className='task-del'; del.innerHTML=ICO.trash;
    del.addEventListener('click',()=>{tasks.splice(idx,1);save();renderTasks();});
    li.appendChild(chk);li.appendChild(span);li.appendChild(del); list.appendChild(li);
  });
}
document.getElementById('btn-add-task').addEventListener('click',addTask);
document.getElementById('task-input').addEventListener('keydown',e=>{if(e.key==='Enter')addTask();});
function addTask(){
  const inp=document.getElementById('task-input'),text=inp.value.trim(); if(!text)return;
  tasks.unshift({id:Date.now().toString(),text,done:false,priority:document.getElementById('task-priority').value});
  inp.value=''; save(); renderTasks();
}

// ══ EXAMS ══════════════════════════════════════
function examUrgency(dateStr){
  const now=new Date(); now.setHours(0,0,0,0);
  const ex=new Date(dateStr+'T00:00:00'); const diff=Math.ceil((ex-now)/(1000*60*60*24));
  if(diff<0)return{cls:'urg-done',label:'Pasado'};
  if(diff===0)return{cls:'urg-high',label:'¡Hoy!'};
  if(diff<=3)return{cls:'urg-high',label:`${diff}d`};
  if(diff<=7)return{cls:'urg-mid',label:`${diff}d`};
  return{cls:'urg-low',label:`${diff}d`};
}

function fmtDate(dateStr){
  const [y,m,d]=dateStr.split('-').map(Number);
  return new Date(y,m-1,d).toLocaleDateString('es-MX',{weekday:'long',day:'numeric',month:'long',year:'numeric'});
}

function openExamModal(examId=null){
  editingExamId=examId;
  const title=document.getElementById('exam-modal-title');
  const fields={subject:'',date:'',time:'09:00',room:'',teacher:'',type:'Parcial',notes:''};

  if(examId){
    const ex=exams.find(e=>e.id===examId);
    if(ex){
      title.textContent='Editar examen';
      Object.keys(fields).forEach(k=>{if(ex[k]!==undefined)fields[k]=ex[k];});
    }
  } else { title.textContent='Nuevo examen'; }

  document.getElementById('exam-subject').value=fields.subject;
  document.getElementById('exam-date').value=fields.date;
  document.getElementById('exam-time').value=fields.time;
  document.getElementById('exam-room').value=fields.room;
  document.getElementById('exam-teacher').value=fields.teacher;
  document.getElementById('exam-type').value=fields.type;
  document.getElementById('exam-notes').value=fields.notes;
  document.getElementById('exam-modal-overlay').classList.add('open');
  document.getElementById('exam-subject').focus();
}

function closeExamModal(){document.getElementById('exam-modal-overlay').classList.remove('open');editingExamId=null;}
document.getElementById('open-exam-modal-main').addEventListener('click',()=>openExamModal());
document.getElementById('open-exam-modal-side').addEventListener('click',()=>openExamModal());
document.getElementById('close-exam-modal-btn').addEventListener('click',closeExamModal);
document.getElementById('exam-modal-overlay').addEventListener('click',e=>{if(e.target===e.currentTarget)closeExamModal();});

document.getElementById('save-exam-btn').addEventListener('click',()=>{
  const subject=document.getElementById('exam-subject').value.trim();
  const date=document.getElementById('exam-date').value;
  if(!subject||!date)return;
  const data={
    id:editingExamId||Date.now().toString(),
    subject, date,
    time:document.getElementById('exam-time').value,
    room:document.getElementById('exam-room').value.trim(),
    teacher:document.getElementById('exam-teacher').value.trim(),
    type:document.getElementById('exam-type').value,
    notes:document.getElementById('exam-notes').value.trim(),
  };
  if(editingExamId){const idx=exams.findIndex(e=>e.id===editingExamId);if(idx!==-1)exams[idx]=data;}
  else exams.push(data);
  exams.sort((a,b)=>a.date.localeCompare(b.date));
  save(); closeExamModal(); renderExams(); renderExamsSide(); renderCalendar();
});

// Exams filter
document.querySelectorAll('#exams-filter .filter-chip').forEach(chip=>chip.addEventListener('click',()=>{
  document.querySelectorAll('#exams-filter .filter-chip').forEach(c=>c.classList.remove('active'));
  chip.classList.add('active'); activeExamFilter=chip.dataset.efilter; renderExams();
}));

function renderExams(){
  const body=document.getElementById('exams-body'); body.innerHTML='';
  const now=new Date(); now.setHours(0,0,0,0);

  let filtered=exams;
  if(activeExamFilter==='upcoming') filtered=exams.filter(e=>new Date(e.date+'T00:00:00')>=now);
  else if(activeExamFilter==='week'){
    const week=new Date(now); week.setDate(week.getDate()+7);
    filtered=exams.filter(e=>{const d=new Date(e.date+'T00:00:00');return d>=now&&d<=week;});
  }
  else if(activeExamFilter==='passed') filtered=exams.filter(e=>new Date(e.date+'T00:00:00')<now);

  // Stats
  const total=exams.filter(e=>new Date(e.date+'T00:00:00')>=now).length;
  const urgent=exams.filter(e=>{const d=new Date(e.date+'T00:00:00');const diff=Math.ceil((d-now)/(864e5));return diff>=0&&diff<=3;}).length;
  const thisWeek=exams.filter(e=>{const d=new Date(e.date+'T00:00:00');const diff=Math.ceil((d-now)/(864e5));return diff>=0&&diff<=7;}).length;

  const stats=document.createElement('div'); stats.className='exam-stats';
  stats.innerHTML=`
    <div class="stat-card urgent"><div class="stat-num">${urgent}</div><div class="stat-label">${ICO.alert} En los próximos 3 días</div></div>
    <div class="stat-card upcoming"><div class="stat-num">${thisWeek}</div><div class="stat-label">Esta semana</div></div>
    <div class="stat-card all"><div class="stat-num">${total}</div><div class="stat-label">Pendientes totales</div></div>`;
  body.appendChild(stats);

  if(!filtered.length){
    const em=document.createElement('div'); em.className='empty-state';
    em.style.gridColumn='1/-1';
    em.innerHTML=`<svg width="56" height="56" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1">
      <path d="M22 10v6M2 10l10-5 10 5-10 5z"/><path d="M6 12v5c3 3 9 3 12 0v-5"/></svg>
      <p>${activeExamFilter!=='all'?'Sin exámenes en este rango':'Aún no tienes exámenes registrados'}</p>`;
    body.appendChild(em); return;
  }

  const grid=document.createElement('div'); grid.className='exams-grid';

  filtered.forEach(ex=>{
    const urg=examUrgency(ex.date);
    const card=document.createElement('div'); card.className=`exam-card ${urg.cls}`;
    card.innerHTML=`
      <div class="exam-card-top">
        <div class="exam-subject">${esc(ex.subject)}</div>
        <div class="exam-countdown">${urg.label}</div>
      </div>
      <div class="exam-type-badge">${esc(ex.type)}</div>
      <div class="exam-details">
        <div class="exam-detail-row">
          <span class="detail-icon">${ICO.calendar}</span>
          <span class="detail-label">Fecha</span>
          <span class="detail-val">${fmtDate(ex.date)} · ${ex.time}</span>
        </div>
        ${ex.room?`<div class="exam-detail-row">
          <span class="detail-icon">${ICO.map}</span>
          <span class="detail-label">Aula</span>
          <span class="detail-val">${esc(ex.room)}</span>
        </div>`:''}
        ${ex.teacher?`<div class="exam-detail-row">
          <span class="detail-icon">${ICO.user}</span>
          <span class="detail-label">Maestro</span>
          <span class="detail-val">${esc(ex.teacher)}</span>
        </div>`:''}
        ${ex.notes?`<div class="exam-notes-text">${ICO.book} ${esc(ex.notes)}</div>`:''}
      </div>
      <div class="exam-card-actions">
        <button class="exam-action-btn" data-edit="${ex.id}">${ICO.edit} Editar</button>
        <button class="exam-action-btn danger" data-del="${ex.id}">${ICO.trash} Eliminar</button>
      </div>`;

    card.querySelector('[data-edit]').addEventListener('click',()=>openExamModal(ex.id));
    card.querySelector('[data-del]').addEventListener('click',()=>{
      exams=exams.filter(e=>e.id!==ex.id);
      save(); renderExams(); renderExamsSide(); renderCalendar();
    });
    grid.appendChild(card);
  });
  body.appendChild(grid);
}

function renderExamsSide(){
  const list=document.getElementById('exams-side-list'); list.innerHTML='';
  const now=new Date(); now.setHours(0,0,0,0);
  const upcoming=exams.filter(e=>new Date(e.date+'T00:00:00')>=now).slice(0,10);

  if(!upcoming.length){
    list.innerHTML=`<div style="text-align:center;padding:1.5rem 0;color:var(--muted);font-size:.75rem;">Sin exámenes próximos</div>`;
    return;
  }
  upcoming.forEach(ex=>{
    const urg=examUrgency(ex.date);
    const card=document.createElement('div'); card.className=`exam-side-card ${urg.cls}`;
    card.innerHTML=`
      <div class="exam-side-subject">${esc(ex.subject)}</div>
      <div class="exam-side-meta">
        ${ex.room?`${ICO.map} ${esc(ex.room)}`:''}
        ${ex.teacher?`${ICO.user} ${esc(ex.teacher)}`:''}
      </div>
      <span class="exam-side-countdown">${urg.label} · ${fmtDate(ex.date)}</span>
      <button class="side-del" data-id="${ex.id}">${ICO.trash}</button>`;
    card.addEventListener('click',e=>{
      if(e.target.closest('.side-del'))return;
      document.querySelectorAll('.rtab').forEach(x=>x.classList.remove('active'));
      document.querySelector('[data-rtab="exams"]').classList.add('active');
      document.getElementById('panel-schedule').classList.add('hidden');
      document.getElementById('panel-exams').classList.remove('hidden');
      document.getElementById('panel-notes').classList.add('hidden');
    });
    card.querySelector('.side-del').addEventListener('click',e=>{
      e.stopPropagation(); exams=exams.filter(ex2=>ex2.id!==ex.id);
      save(); renderExams(); renderExamsSide(); renderCalendar();
    });
    list.appendChild(card);
  });
}

// ══ NOTES ══════════════════════════════════════
const NC=['nc-0','nc-1','nc-2','nc-3','nc-4','nc-5'];
const TAG_UI={'General':'📚 General','Matemáticas':'📐 Matemáticas','Ciencias':'🔬 Ciencias','Historia':'🏛️ Historia','Literatura':'📖 Literatura','Idea':'💡 Idea'};

function openNoteModal(noteId=null){
  editingNoteId=noteId;
  const titleEl=document.getElementById('note-modal-title');
  const ti=document.getElementById('note-title-input'), bi=document.getElementById('note-body-input');
  selectedNoteColor=5; selectedNoteTag='General';
  document.querySelectorAll('#note-color-picker .color-opt').forEach(o=>o.classList.toggle('active',parseInt(o.dataset.c)===5));
  document.querySelectorAll('#note-tags-input .tag-chip').forEach(t=>t.classList.toggle('selected',t.dataset.tag==='General'));

  if(noteId){
    const note=notes.find(n=>n.id===noteId);
    if(note){
      titleEl.textContent='Editar nota'; ti.value=note.title; bi.value=note.body||'';
      selectedNoteColor=note.color??5; selectedNoteTag=note.tag??'General';
      document.querySelectorAll('#note-color-picker .color-opt').forEach(o=>o.classList.toggle('active',parseInt(o.dataset.c)===selectedNoteColor));
      document.querySelectorAll('#note-tags-input .tag-chip').forEach(t=>t.classList.toggle('selected',t.dataset.tag===selectedNoteTag));
    }
  } else { titleEl.textContent='Nueva nota'; ti.value=''; bi.value=''; }
  document.getElementById('note-modal-overlay').classList.add('open'); ti.focus();
}
function closeNoteModal(){document.getElementById('note-modal-overlay').classList.remove('open');editingNoteId=null;}

document.getElementById('open-note-modal-main').addEventListener('click',()=>openNoteModal());
document.getElementById('open-note-modal-side').addEventListener('click',()=>openNoteModal());
document.getElementById('close-note-modal-btn').addEventListener('click',closeNoteModal);
document.getElementById('note-modal-overlay').addEventListener('click',e=>{if(e.target===e.currentTarget)closeNoteModal();});

document.querySelectorAll('#note-color-picker .color-opt').forEach(opt=>opt.addEventListener('click',()=>{
  document.querySelectorAll('#note-color-picker .color-opt').forEach(o=>o.classList.remove('active'));
  opt.classList.add('active'); selectedNoteColor=parseInt(opt.dataset.c);
}));
document.querySelectorAll('#note-tags-input .tag-chip').forEach(chip=>chip.addEventListener('click',()=>{
  document.querySelectorAll('#note-tags-input .tag-chip').forEach(c=>c.classList.remove('selected'));
  chip.classList.add('selected'); selectedNoteTag=chip.dataset.tag;
}));

document.getElementById('save-note-btn').addEventListener('click',()=>{
  const title=document.getElementById('note-title-input').value.trim(), body=document.getElementById('note-body-input').value.trim();
  if(!title)return;
  const now=new Date().toLocaleDateString('es-MX',{day:'numeric',month:'short',year:'numeric'});
  if(editingNoteId){const idx=notes.findIndex(n=>n.id===editingNoteId);if(idx!==-1)notes[idx]={...notes[idx],title,body,color:selectedNoteColor,tag:selectedNoteTag,date:now};}
  else notes.unshift({id:Date.now().toString(),title,body,color:selectedNoteColor,tag:selectedNoteTag,date:now});
  save(); closeNoteModal(); renderNotes(); renderNotesSide();
});

document.querySelectorAll('#notes-filter-bar .filter-chip').forEach(chip=>chip.addEventListener('click',()=>{
  document.querySelectorAll('#notes-filter-bar .filter-chip').forEach(c=>c.classList.remove('active'));
  chip.classList.add('active'); activeNoteFilter=chip.dataset.filter; renderNotes();
}));
document.getElementById('notes-search').addEventListener('input',e=>{noteSearchQ=e.target.value.trim().toLowerCase();renderNotes();});

function renderNotes(){
  const grid=document.getElementById('notes-grid'); grid.innerHTML='';
  let f=notes;
  if(activeNoteFilter) f=f.filter(n=>n.tag===activeNoteFilter);
  if(noteSearchQ) f=f.filter(n=>n.title.toLowerCase().includes(noteSearchQ)||(n.body||'').toLowerCase().includes(noteSearchQ));

  if(!f.length){
    const em=document.createElement('div');em.className='empty-state';
    em.innerHTML=`<svg width="52" height="52" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1"><path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"/><polyline points="14 2 14 8 20 8"/><line x1="16" y1="13" x2="8" y2="13"/><line x1="16" y1="17" x2="8" y2="17"/></svg>
      <p>${noteSearchQ||activeNoteFilter?'Sin resultados':'Aún no tienes notas'}</p>`;
    grid.appendChild(em); return;
  }
  f.forEach(note=>{
    const card=document.createElement('div'); card.className=`note-card ${NC[note.color??5]}`;
    card.innerHTML=`<div class="note-card-header"><div class="note-card-title">${esc(note.title)}</div>
      <button class="note-card-del" data-id="${note.id}">${ICO.trash}</button></div>
      <div class="note-card-body">${esc(note.body||'Sin contenido')}</div>
      <div class="note-card-footer"><span class="note-card-tag">${TAG_UI[note.tag]||note.tag||'📚 General'}</span>
      <span class="note-card-date">${note.date||''}</span></div>`;
    card.addEventListener('click',e=>{if(e.target.closest('.note-card-del'))return;openNoteView(note.id);});
    card.querySelector('.note-card-del').addEventListener('click',e=>{
      e.stopPropagation(); notes=notes.filter(n=>n.id!==note.id); save(); renderNotes(); renderNotesSide();
    });
    grid.appendChild(card);
  });
}

function renderNotesSide(){
  const list=document.getElementById('notes-side-list'); list.innerHTML='';
  if(!notes.length){list.innerHTML=`<div style="text-align:center;padding:1.5rem 0;color:var(--muted);font-size:.75rem;">Sin notas aún</div>`;return;}
  notes.forEach(note=>{
    const card=document.createElement('div'); card.className=`note-side-card ${NC[note.color??5]}`;
    card.innerHTML=`<div class="note-side-title">${esc(note.title)}</div>
      <div class="note-side-preview">${esc(note.body||'Sin contenido')}</div>
      <button class="side-del" data-id="${note.id}">${ICO.trash}</button>`;
    card.addEventListener('click',e=>{
      if(e.target.closest('.side-del'))return;
      document.querySelectorAll('.rtab').forEach(x=>x.classList.remove('active'));
      document.querySelector('[data-rtab="notes"]').classList.add('active');
      document.getElementById('panel-schedule').classList.add('hidden');
      document.getElementById('panel-exams').classList.add('hidden');
      document.getElementById('panel-notes').classList.remove('hidden');
      openNoteView(note.id);
    });
    card.querySelector('.side-del').addEventListener('click',e=>{
      e.stopPropagation(); notes=notes.filter(n=>n.id!==note.id); save(); renderNotes(); renderNotesSide();
    });
    list.appendChild(card);
  });
}

function openNoteView(noteId){
  const note=notes.find(n=>n.id===noteId); if(!note)return;
  viewingNoteId=noteId;
  document.getElementById('nv-title').textContent=note.title;
  document.getElementById('nv-body').textContent=note.body||'';
  document.getElementById('nv-tag').textContent=TAG_UI[note.tag]||note.tag||'';
  document.getElementById('nv-date').textContent=note.date||'';
  document.getElementById('note-view-overlay').classList.add('open');
}
document.getElementById('close-note-view-btn').addEventListener('click',()=>document.getElementById('note-view-overlay').classList.remove('open'));
document.getElementById('note-view-overlay').addEventListener('click',e=>{if(e.target===e.currentTarget)document.getElementById('note-view-overlay').classList.remove('open');});
document.getElementById('edit-note-btn').addEventListener('click',()=>{document.getElementById('note-view-overlay').classList.remove('open');openNoteModal(viewingNoteId);});

// ══ HELPERS ══════════════════════════════════════
function esc(s){return(s||'').replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;').replace(/"/g,'&quot;');}

// ══ INIT ══════════════════════════════════════
initCalendar();
renderTasks();
renderNotes();
renderNotesSide();
renderExams();
renderExamsSide();
</script>
</body>
</html>
