# 检验报告

外观尺寸检测
---------------------------------------------------------------------------
### InspectionExterior Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`inspection_id`                     | int       | No   | 
`is_consistent`                     | int       | No   | 描述是否一致
`is_intact`                         | int       | No   | 包装完整性

图像检测
---------------------------------------------------------------------------
整合之前的显微镜和电镜检测。

### InspectionGraphics Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`inspection_id`                     | int       | No   | 
`media_id`                          | int       | No   | 
`device`                            | int       | No   | Lookup `graphics-device`
`shape`                             | int       | Yes  | Lookup `powder-shape`
`mignification`                     | int       | Yes  | Lookup `sem-mignification`
