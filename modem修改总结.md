<h1><center>modem修改总结</center></h1>

## 一、custom_imc_config.c

### 1、Volte roaming

```
nvram_ims_profile_ptr->imc_config.ims_roaming_mode = 1;  // enbale volte roaming;

nvram_ims_profile_ptr->imc_config.ignore_media_qos_check        = 1;
```

```
cfg.wans_cfg.wans_ims_roaming_barring_enable        = KAL_FALSE;
```

### 2、user_agent

```
#ifdef __VQ287_2871_ORANGE_SV__
    kal_snprintf((char *)ua->user_agent, 128-1, "PRD-IR92/15 term-Mobiwire/HAPI52-20220322 device-type/smart-phone mno-custom/208001");
#else
    kal_snprintf((char *)ua->user_agent, 128-1, "VoLTE/WFC UA");
#endif
```

### 3、SMSoIP

```
nvram_ims_profile_ptr->ua_config.sms_network_types              = 0x03;   //wifi and lte

nvram_ims_profile_ptr->imc_config.sms_support                   = 1;
```

## 二、sbp_nvram_vdm_config.c

### 1、紧急号码网络选择

```
nvram_vdm_ads_profile_ptr->profile_common.ads_cellular_prefer_check_vowifi_first_if_ims_reg_on_wifi = 1;

nvram_vdm_ads_profile_ptr->profile_emerg.prefer_cs_when_volte_unreged = 1;

nvram_vdm_ads_profile_ptr->profile_normal.need_check_emb_eutran_for_380_to_normal_cs = 1;

nvram_vdm_ads_profile_ptr->profile_normal.need_check_emb_eutran_for_380_normal_cs_to_ims = 1;
```

