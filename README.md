# Warehouse Operations Forecasting

Customer Order & Brand Inbound Capacity Planning

## Overview
This project builds forecasting pipelines to support warehouse operations planning by modeling both outbound demand and inbound supply uncertainty.

It focuses on two complementary problems:
- Customer orders forecasting (Online & Marketplace) to support staffing and outbound capacity
- Brand inbound delivery forecasting to support receiving, dock scheduling, and storage planning 

All volumes are shown on normalized scales to protect data while preserving trends, seasonality, and relative behavior.

## Business Problem

Warehouse capacity planning requires reliable forecasts across multiple, interdependent flows:
- Outbound demand (customer orders) drives picking, packing, and reverse logistics
- Inbound supply (brand deliveries) is roughly planned by the brands in advance but is done pointing to a wide date range, creating uncertainty for receiving operations

The goal is not exact prediction, but actionable capacity ranges that can be refreshed regularly and adjusted to operational constraints.

## Data & Granularity

Weekly aggregation (W-FRI), aligned with warehouse planning cycles  

Two outbound demand streams:
- Online (B2C)
- Marketplace (B2B)

Inbound data includes:
- Planned delivery windows
- Historically shipped quantities

## Methodology
### Customer Orders Forecast

Time series decomposition identified:
- strong volatility
- half-year seasonality (~26 weeks)
- structural trend shifts

Holt–Winters Exponential Smoothing models evaluated:
- Additive vs. multiplicative seasonality
- Damped trend

Model selection based on:
- MAE, RMSE
- MAPE & WAPE
- Bias

Selected approach:  
Multiplicative seasonality with damped trend, balancing accuracy and interpretability.

📄 Notebook: [Customer Orders Forecast Notebook](notebooks/01_customer_orders_forecast.ipynb)

### Brand Inbound Delivery Forecast

Inbound workload is driven by planned quantities, but actual shipments consistently under-realize plans.

This was modeled in two steps:
1. Forecast planned inbound volumes using scaled exponential smoothing
2. Estimate realized shipments using a stable historical shipment-to-plan ratio

Key observations:
- Planned volumes act as a guide, not expected workload
- Shipment-to-plan ratios are noisy week-to-week but stable in aggregate
- Ratio-based adjustment is more robust than forcing complex models

📄 Notebook: [Brand Inbound Forecast Notebook](notebooks/02_brand_inbound_forecast.ipynb)

## Results (High Level)
Forecasts capture major trend and seasonal dynamics across all streams

Accuracy is realistic given operational volatility:
- Orders: moderate error, stable bias
- Inbound: higher uncertainty, but reliable directional guidance

Outputs are suitable for:
- staffing buffers
- peak-period contingency planning
- weekly rolling updates

## Operational Interpretation

- Forecasts should be used as ranges, not point estimates
- Peak periods require additional contingency capacity
- Inbound receiving should be planned against expected realized volume, not planned quantities
- Models should be refreshed weekly during high-season periods

## Limitations & Next Steps

- Explicit cost-based optimization (under- vs over-capacity)
- Separate peak vs non-peak regime modeling
- Integration with staffing and shift-planning constraints
- Forecast monitoring and recalibration over time

## Repository Structure

```
├── notebooks/
│   ├── 01_customer_orders_forecast.ipynb
│   └── 02_brand_inbound_forecast.ipynb
├── reports/
│   ├── 01_customer_orders_forecast.pdf
│   └── 02_brand_inbound_forecast.pdf
├── README.md
└── requirements.txt
```

```bash
pip install -r requirements.txt
