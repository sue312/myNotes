<h1><center>modem log</center></h1>

一、radio log或modem log system trace窗口搜关键字确认register status
Before 93 modem:
“CREG”:check the register status of CS domain
“CGREG”:check the register status of PS domain
“CEREG”:check the register status of EPS domain

93 and after modem:
“EREG”:check the register status of CS domain
“EGREG”:check the register status of PS/EPS domain

二、Anite仪表log分析fail case，搜索关键字：Verdict\inconclusive\mismatch\not match\not equal\CMD\CCH-MESSAGE
Verditc:to find error location
CCH-Message：to find all AS air message except system information
CMD=:find the SS request UE to do

三、R&S仪表log分析fail case, 搜索关键字：Verdict\mismatch\differences\MMI command
From the verdict window to find the fail point，then change to message tree window to find the detail info.(ttcn_verdict.log)
"MMI command":Get SS ask UE to do

四、Anritsu仪表log分析fail case, 搜索关键字：Fail\UT_SYSTEM_REQ
"UT_SYSTEM_REQ":Get SS ask UE to do

五、modem log搜索"test mode"确定FTA mode是否打开
PS_CONF_TEST_FTA->FTA enable
PS_CONF_TEST_NONE->FTA disable



六、radio log搜索“Broadcasting ServiceState” 查看Service State

![img](http://192.168.3.86:8989/wcp/actionImg/Publoadfile.do?id=402883cd6e97399f016eab718f4100bd)

![img](http://192.168.3.86:8989/wcp/actionImg/Publoadfile.do?id=402883cd6e97399f016eab745a4c00be)



七、modem log 搜索EARFCN(4G)、UARFCN(3G)、ARFCN(2G)，确定服务小区、band信息



1、搜索 “init band_list”可查看样机支持的band信息；

2、搜索”init srch_req”可查看样机当前搜到的band；

3、搜素’Power scan completed on’可查看band的频率和接收的信号强度；

4、搜索’serv cell’可查看样机当前驻留在哪个band上



八、modem log

1、在PS Integrated窗口
1)、搜索“[RAC] UE Mode =”，查看UE mode (UE_MODE_CS_PS_MODE_1/UE_MODE_PS_MODE_1/UE_MODE_CS_PS_MODE_2/UE_MODE_PS_MODE_2)
2)、搜索“[MM] Current request service:” 查看UE某节点所处的service (CS_PS_DOMAIN/PS_DOMAIN/CS_DOMAIN)
3)、搜索“PLMN_SEARCH”查看PLMN　search procedure;MSG_ID_NWSEL_EVAL_PLMN_SEARCH_CNF表示4G PLMN search结果,MSG_ID_NWSEL_MM_PLMN_SEARCH_CNF表示2/3G PLMN search结果，MSG_ID_L4CRAC_PLMN_SEARCH_CNF表示2/3/4G　PLMN search结果。
4)、搜索“PLMN_LIST”查看Modem手动搜网的过程（由AP的AT+COPS=?发起）;MSG_ID_NWSEL_EVAL_PLMN_LIST_CNF表示4G搜到的PLMN LIST,MSG_ID_MM_RATCM_PLMN_LIST_REQF表示2/3G搜到的PLMN LIST，MSG_ID_L4CRAC_PLMN_LIST_CNF表示2/3/4G搜到的PLMN LIST。
2、在System Trace窗口
1)、搜索“ATD”,表示UE主动拨打的普通电话
2)、搜索“ATDE”,表示UE主动拨打的紧急电话
3)、搜索“+ECPI”,查看call process information
3、在OTA Message窗口
搜索“SystemInformationBlockType1”，即SIB1可查看当前可选小区、PLMN、频段等信息