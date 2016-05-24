# HO_reports
LTE HO Reports

/***rsiriya 033016***/

{ "category": "KPI Dash",
  "name": "HO reports",
  "subtitle": "LTE Sector",
  "description": "LTE Sector",
  "tablecellwidth":400,
  "tablecellmargin":5,
  "private": false,
  "template": "dashboard",
  "dashlayout": {
    "type": "table",
    "columns": 4,
    "rows": 2
  },
  "reports": [
    {
      "type":"HTML",
      "title":"",
      "width":55,
      "rowspan":2,
      "backcolor":"#4E4E4E",
      "html":"<div style=\"position:absolute;top:0px;height:100%;width:40px;\"><div style=\"transform: translateY(-50%) rotate(270deg);margin-left:-80px;position:absolute;top:50%;text-align:center;color:rgb(255,255,255);white-space:nowrap;font-family:tahoma;font-size:20px;\"><b>IntraFreq Handover </b></div></div>"
    },

    {
      "type":"TABLE",
      "title":"<b>Src-Tgt with IntraF HO fail > 30% and IntraF HO Relative Att > 10%</b><br><i>For Last available day</i>",
      "paging":true,
      "pagingsize":15,
      "width":1200,
      "rowspan":2,
      "grouping":true,
      "db":{
        "dbtype":"MYSQL",
        "dbname":"kpi_tmo_sf",
        "server":"TTSSVR71",
        "user":"user_read",
        "pass":"tts1234",
        "port":3306,
        "sql":"SELECT A.SourceSector AS Src_cell, A.HL_TargetSector AS Tgt_Cell, A.IntraF_Prep_Attempts AS IntraF_Prep_attempts,ROUND(CASE WHEN(B.Sum_IntraF_Prep_Attempts) = 0 THEN 0 ELSE(A.IntraF_Prep_Attempts) /(B.Sum_IntraF_Prep_Attempts) * 100 END,2) AS IntraF_HO_Rel_Att_Pct , A.IntraF_HO_fail_rate AS IntraF_HO_fail_pct, A.IntraF_HO_Prep_fail AS IntraF_HO_Prep_fail, A.IntraF_HO_Exe_fail AS IntraF_HO_Exe_fail, DATE_FORMAT(A.HL_date,'%Y-%m-%d') AS Date  From (SELECT SourceSector, HL_TargetSector, PMHOPREPATTLTEINTRAF AS IntraF_Prep_Attempts, ROUND(CASE WHEN(PMHOPREPATTLTEINTRAF = 0) THEN 0 ELSE (100*LTEMOBILITYFAILURESINTRAFREQC/PMHOPREPATTLTEINTRAF) END,2) as IntraF_HO_fail_rate, (pmHoPrepAttLteIntraF-pmHoPrepSuccLteIntraF) AS IntraF_HO_Prep_fail, (pmHoExeAttLteIntraF-pmHoExeSuccLteIntraF) AS IntraF_HO_Exe_fail, HL_date FROM  lte_lte_ho_eric WHERE  HL_Date = (select max(HL_DATE) FROM lte_lte_ho_eric) and HL_IsIntraFreq = 1 ) as A, (SELECT SourceSector, SUM(pmHoPrepAttLteIntraF) AS Sum_IntraF_Prep_Attempts FROM  lte_lte_ho_eric WHERE HL_Date = (select max(HL_DATE) FROM lte_lte_ho_eric) GROUP BY SourceSector) AS B WHERE A.SourceSector = B.SourceSector AND A.IntraF_HO_fail_rate > 30 AND ROUND(CASE WHEN(B.Sum_IntraF_Prep_Attempts) = 0 THEN 0 ELSE(A.IntraF_Prep_Attempts) /(B.Sum_IntraF_Prep_Attempts) * 100 END, 2) > 10.0 ORDER BY A.IntraF_HO_fail_rate DESC, A.IntraF_Prep_attempts DESC"
        
          },
      "fields":[
        {"name":"Src_cell"},
        {"name":"Tgt_Cell"},
        {"name":"IntraF_Prep_attempts"},
        {"name":"IntraF_HO_Rel_Att_Pct"},
        {"name":"IntraF_HO_fail_pct"}, 
        {"name":"IntraF_HO_Prep_fail"},
        {"name":"IntraF_HO_Exe_fail"},
        {"name":"Date"}
      ],
      "columns":[
        { "xtype":"actioncolumn",
          "menuDisabled": true,
          "sortable": false,
          "hideable": false,
          "width":40,
          "items": [
            {
              "icon": "resources/icons/marker_locate_32.png",
              "tooltip":"Locate cell",
              "padding":"0 10 0 0",
              "handler": function(grid, rowIndex, colIndex) {
                var rec = grid.getStore().getAt(rowIndex);
                if(rec){
                  var tech = 'LTE';
                  var cell = rec.get('Src_cell');
                  optpcs.util.MapUtil.runGridCellActionCol({option:0,tech:tech,cell:cell});
                }
              }
          },{
              "icon": "resources/icons/header_dashboard_16.png",
              "tooltip":"Show cell dashboard",
              "padding":"0 10 0 0",
              "handler": function(grid, rowIndex, colIndex) {
                var rec = grid.getStore().getAt(rowIndex);
                if(rec){
                  var tech = 'LTE';
                  var cell = rec.get('Src_cell');
                  optpcs.util.MapUtil.runGridCellActionCol({option:1,tech:tech,cell:cell});
                }
              }
          }]
        },
        {"flex":1,"width":150,"dataIndex":"Src_cell","text":"<b>Src_cell"},
        {"width":150,"dataIndex":"Tgt_Cell","text":"<b>Tgt_Cell","align":"center"},
        {"width":160,"dataIndex":"IntraF_HO_Rel_Att_Pct","text":"<b>IntraF_HO_Rel_Att_Pct","align":"center"},
        {"width":160,"dataIndex":"IntraF_Prep_attempts","text":"<b>IntraF_Prep_attempts","align":"center"},
        {"width":155,"dataIndex":"IntraF_HO_Prep_fail","text":"<b>IntraF_HO_Prep_fail","align":"center"},
        {"width":155,"dataIndex":"IntraF_HO_Exe_fail","text":"<b>IntraF_HO_Exe_fail","align":"center"},
        {"width":155,"dataIndex":"IntraF_HO_fail_pct","text":"<b>IntraF_HO_fail_pct","align":"center","style":"background-color:#FF6600"},
        {"width":100,"dataIndex":"Date","text":"<b>Date","align":"center"}
      ]
    },
    
    {
      "type":"HTML",
      "title":"",
      "width":55,
      "rowspan":2,
      "backcolor":"#4E4E4E",
      "html":"<div style=\"position:absolute;top:0px;height:100%;width:40px;\"><div style=\"transform: translateY(-50%) rotate(270deg);margin-left:-80px;position:absolute;top:50%;text-align:center;color:rgb(255,255,255);white-space:nowrap;font-family:tahoma;font-size:20px;\"><b>InterFreq Handover </b></div></div>"
    },
    
    {
      "type":"TABLE",
      "title":"<b>Src-Tgt with InterF HO fail > 30% and InterF HO Relative Att > 10%</b><br><i>For Last available day</i>",
      "paging":true,
      "pagingsize":15,
      "width":1200,
      "rowspan":2,
      "grouping":true,
      "db":{
        "dbtype":"MYSQL",
        "dbname":"kpi_tmo_sf",
        "server":"TTSSVR71",
        "user":"user_read",
        "pass":"tts1234",
        "port":3306,
        "sql":"SELECT A.SourceSector AS Src_cell, A.HL_TargetSector AS Tgt_Cell, A.InterF_Prep_Attempts AS InterF_Prep_attempts,ROUND(CASE WHEN(B.Sum_InterF_Prep_Attempts) = 0 THEN 0 ELSE(A.InterF_Prep_Attempts) /(B.Sum_InterF_Prep_Attempts) * 100 END,2) AS InterF_HO_Rel_Att_Pct , A.InterF_HO_fail_rate AS InterF_HO_fail_pct, A.InterF_HO_Prep_fail AS InterF_HO_Prep_fail, A.InterF_HO_Exe_fail AS InterF_HO_Exe_fail, DATE_FORMAT(A.HL_date,'%Y-%m-%d') AS Date  From (SELECT SourceSector, HL_TargetSector, PMHOPREPATTLTEINTERF AS InterF_Prep_Attempts, ROUND(CASE WHEN(PMHOPREPATTLTEINTERF = 0) THEN 0 ELSE (100*LTEMOBILITYFAILURESINTERFREQC/PMHOPREPATTLTEINTERF) END,2) as InterF_HO_fail_rate, (pmHoPrepAttLteInterF-pmHoPrepSuccLteInterF) AS InterF_HO_Prep_fail, (pmHoExeAttLteInterF-pmHoExeSuccLteInterF) AS InterF_HO_Exe_fail, HL_date FROM  lte_lte_ho_eric WHERE  HL_Date = (select max(HL_DATE) FROM lte_lte_ho_eric) and HL_IsIntraFreq <> 1 ) as A, (SELECT SourceSector, SUM(pmHoPrepAttLteInterF) AS Sum_InterF_Prep_Attempts FROM  lte_lte_ho_eric WHERE HL_Date = (select max(HL_DATE) FROM lte_lte_ho_eric) GROUP BY SourceSector) AS B WHERE A.SourceSector = B.SourceSector AND A.InterF_HO_fail_rate > 30.0 AND ROUND(CASE WHEN(B.Sum_InterF_Prep_Attempts) = 0 THEN 0 ELSE(A.InterF_Prep_Attempts) /(B.Sum_InterF_Prep_Attempts) * 100 END, 2) > 10.0 ORDER BY A.InterF_HO_fail_rate DESC,A.InterF_Prep_Attempts DESC"
        
          },
      "fields":[
        {"name":"Src_cell"},
        {"name":"Tgt_Cell"},
        {"name":"InterF_Prep_attempts"},
        {"name":"InterF_HO_Rel_Att_Pct"},
        {"name":"InterF_HO_fail_pct"}, 
        {"name":"InterF_HO_Prep_fail"},
        {"name":"InterF_HO_Exe_fail"},
        {"name":"Date"}
      ],
      "columns":[
        { "xtype":"actioncolumn",
          "menuDisabled": true,
          "sortable": false,
          "hideable": false,
          "width":40,
          "items": [
            {
              "icon": "resources/icons/marker_locate_32.png",
              "tooltip":"Locate cell",
              "padding":"0 10 0 0",
              "handler": function(grid, rowIndex, colIndex) {
                var rec = grid.getStore().getAt(rowIndex);
                if(rec){
                  var tech = 'LTE';
                  var cell = rec.get('Src_cell');
                  optpcs.util.MapUtil.runGridCellActionCol({option:0,tech:tech,cell:cell});
                }
              }
          },{
              "icon": "resources/icons/header_dashboard_16.png",
              "tooltip":"Show cell dashboard",
              "padding":"0 10 0 0",
              "handler": function(grid, rowIndex, colIndex) {
                var rec = grid.getStore().getAt(rowIndex);
                if(rec){
                  var tech = 'LTE';
                  var cell = rec.get('Src_cell');
                  optpcs.util.MapUtil.runGridCellActionCol({option:1,tech:tech,cell:cell});
                }
              }
          }]
        },
        {"flex":1,"width":150,"dataIndex":"Src_cell","text":"<b>Src_cell"},
        {"width":150,"dataIndex":"Tgt_Cell","text":"<b>Tgt_Cell","align":"center"},
        {"width":160,"dataIndex":"InterF_HO_Rel_Att_Pct","text":"<b>InterF_HO_Rel_Att_Pct","align":"center"},
        {"width":160,"dataIndex":"InterF_Prep_attempts","text":"<b>InterF_Prep_attempts","align":"center"},
        {"width":155,"dataIndex":"InterF_HO_Prep_fail","text":"<b>InterF_HO_Prep_fail","align":"center"},
        {"width":155,"dataIndex":"InterF_HO_Exe_fail","text":"<b>InterF_HO_Exe_fail","align":"center"},
        {"width":155,"dataIndex":"InterF_HO_fail_pct","text":"<b>InterF_HO_fail_pct","align":"center","style":"background-color:#FF6600"},
        {"width":100,"dataIndex":"Date","text":"<b>Date","align":"center"}
      ]
    }
    
    
    

  ]
}
