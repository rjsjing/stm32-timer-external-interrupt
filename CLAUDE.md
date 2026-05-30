# CLAUDE.md — STM32F103C8T6 定时器外部中断

## 技术栈

- **MCU**: STM32F103C8T6（Cortex-M3, 64KB Flash, 20KB SRAM, 72MHz）
- **库**: ST 标准外设库 v3.5.0
- **构建工具**: Keil MDK uVision5 + EIDE (VSCode 插件 `cl.eide`)
- **调试器**: ST-Link (SWD)

## 目录结构

```
├── Start/           CMSIS 启动 + system_stm32f10x
├── Library/         ST 标准外设库 v3.5.0
├── System/          Delay.c/h — SysTick 延时
│                    Timer.c/h — TIM2 定时中断驱动
├── Hardwera/        OLED 显示 + Key/LED 驱动
├── User/            main.c, stm32f10x_conf.h, stm32f10x_it.c/h
├── build/           EIDE 构建输出
├── Objects/         Keil 编译产物
└── Listings/        编译清单 (.map)
```

## 硬件引脚映射

| 引脚 | 功能 | 连接目标 |
|------|------|----------|
| PB5 | GPIO Out OD | OLED SCL |
| PB6 | GPIO Out OD | OLED SDA |

## Timer 驱动 (System/Timer.c/h)

- `Timer2_Init(arr, psc)` — TIM2 定时中断，arr=9999, psc=7199 → 1s 周期
- 优先级组2, 抢占2, 子优先级0
- 定时公式: T(ms) = (arr+1)*(psc+1)/72000

## 功能说明

TIM2 每秒产生一次中断，ISR 中 Num++。OLED 显示累计秒数和 TIM2->CNT 实时计数值。演示定时器中断 + NVIC 优先级配置。

## 编码规范

- 函数命名: `外设_操作()`
- NVIC 优先级分组: 组2
- 编码: UTF-8，注释用中文

## Git 规范
```
main              ← 稳定分支
提交: feat:/fix:/docs: 格式
```

## 构建命令
```
Keil:  打开 Project.uvprojx → F7 编译 → F8 烧录
EIDE:  Ctrl+Shift+P → "EIDE: Build"
```

## 注意事项
- `USE_STDPERIPH_DRIVER` + `STM32F10X_MD` 编译选项必须定义
- 启动文件 `startup_stm32f10x_md.s`
- TIM2 挂载 APB1, ISR 名: `TIM2_IRQHandler`
