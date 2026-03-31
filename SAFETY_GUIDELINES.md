# ⚠️ Safety Guidelines for Algorithmic Trading System

## 🚨 CRITICAL WARNING

**This system is designed for REAL MONEY TRADING. Improper use can result in SUBSTANTIAL FINANCIAL LOSSES.**

Please read and understand ALL safety guidelines before operating this system.

---

## Table of Contents
1. [Pre-Production Safety Checklist](#1-pre-production-safety-checklist)
2. [Environment Safety Controls](#2-environment-safety-controls)
3. [Risk Management Guidelines](#3-risk-management-guidelines)
4. [Order Safety Mechanisms](#4-order-safety-mechanisms)
5. [Strategy Safety Rules](#5-strategy-safety-rules)
6. [System Circuit Breakers](#6-system-circuit-breakers)
7. [Emergency Procedures](#7-emergency-procedures)
8. [Operational Safety](#8-operational-safety)
9. [Testing Requirements](#9-testing-requirements)
10. [Incident Response](#10-incident-response)

---

## 1. Pre-Production Safety Checklist

### 1.1 Mandatory Testing Phases

✅ **Required Before Live Trading:**

```yaml
testing_phases:
  1_unit_testing:
    coverage: "> 80%"
    focus: "Core logic validation"
    duration: "1 week minimum"

  2_backtesting:
    historical_period: "2 years minimum"
    market_conditions: "Bull, Bear, Sideways"
    validation: "Verify all metrics"
    duration: "2 weeks minimum"

  3_paper_trading:
    duration: "1 month minimum"
    capital: "Same as intended live capital"
    monitoring: "Daily review"
    success_criteria:
      - "Positive returns"
      - "Risk limits never breached"
      - "No system errors"

  4_limited_live:
    duration: "2 weeks minimum"
    capital: "10% of intended capital"
    monitoring: "Real-time"
    stop_condition: "Any significant issue"
```

### 1.2 Pre-Launch Verification

| Component | Verification Required | Sign-off Required |
|-----------|----------------------|-------------------|
| **Risk Limits** | All configured and tested | Risk Manager |
| **Circuit Breakers** | Triggered in test environment | Tech Lead |
| **Kill Switch** | Tested successfully | Operations Head |
| **Backup Systems** | Failover tested | DevOps Lead |
| **Monitoring** | All alerts configured | Operations |
| **Documentation** | Complete and reviewed | All stakeholders |

---

## 2. Environment Safety Controls

### 2.1 Environment Separation

```python
# NEVER bypass environment checks
class EnvironmentSafety:
    @staticmethod
    def verify_environment():
        env = os.getenv('TRADING_ENV')

        if env == 'DEVELOPMENT':
            # Backtesting only - NO live connections
            return BlockAllLiveConnections()

        elif env == 'PAPER':
            # Simulated trading only
            return EnablePaperTrading()

        elif env == 'PRODUCTION':
            # Requires explicit confirmation
            if not confirm_production_trading():
                raise Exception("Production trading not confirmed")
            if not verify_2fa():
                raise Exception("2FA required for production")
            return EnableLiveTrading()

        else:
            raise Exception("Invalid environment")
```

### 2.2 Configuration Safety

| Environment | Live Trading | Real Money | API Keys | Safety Level |
|-------------|--------------|------------|----------|--------------|
| **Development** | ❌ Disabled | ❌ None | Test only | Maximum |
| **Paper** | ✅ Simulated | ❌ Fake | Paper only | High |
| **Production** | ✅ Enabled | ✅ Real | Live | Careful! |

### 2.3 Deployment Safety Gates

```yaml
production_deployment:
  requires:
    - manual_approval: true
    - approvers: ["tech_lead", "risk_manager"]
    - 2fa_verification: true
    - deployment_window: "Non-market hours only"
    - rollback_plan: "Documented and tested"
    - monitoring_active: true
```

---

## 3. Risk Management Guidelines

### 3.1 Mandatory Risk Limits

**ALL strategies MUST have these limits configured:**

```python
MANDATORY_RISK_LIMITS = {
    "max_position_size_pct": 10,      # Max 10% per position
    "max_daily_loss_pct": 2,           # Max 2% daily loss
    "max_drawdown_pct": 15,            # Max 15% drawdown
    "max_consecutive_losses": 5,        # Stop after 5 losses
    "max_leverage": 2,                  # Max 2x leverage
    "stop_loss_required": True,         # Every position needs SL
    "position_limit": 20,               # Max 20 open positions
}
```

### 3.2 Risk Validation Rules

```python
def validate_trade(trade, portfolio):
    """
    NEVER bypass these checks - they prevent catastrophic losses
    """
    checks = [
        check_position_size(trade, portfolio),
        check_daily_loss_limit(portfolio),
        check_drawdown_limit(portfolio),
        check_leverage_limit(portfolio),
        check_margin_requirements(trade, portfolio),
        check_correlation_limits(trade, portfolio),
    ]

    if not all(checks):
        # BLOCK THE TRADE
        log_rejected_trade(trade, checks)
        return False

    return True
```

### 3.3 Progressive Risk Limits

| Trading Phase | Max Position | Daily Loss | Leverage | Monitoring |
|---------------|--------------|------------|----------|------------|
| **Week 1** | 5% | 1% | 1x | Every trade |
| **Week 2-4** | 7% | 1.5% | 1.5x | Hourly |
| **Month 2** | 10% | 2% | 2x | Real-time |
| **Steady State** | Per strategy | Per strategy | Per strategy | Automated |

---

## 4. Order Safety Mechanisms

### 4.1 Order Validation Pipeline

```python
ORDER_VALIDATION_PIPELINE = [
    "1. Symbol validation",
    "2. Market hours check",
    "3. Price sanity check (±20% from LTP)",
    "4. Quantity validation",
    "5. Risk limit check",
    "6. Margin verification",
    "7. Duplicate order check",
    "8. Rate limit check",
]

# EVERY order must pass ALL checks
```

### 4.2 Duplicate Order Prevention

```python
class OrderSafety:
    def __init__(self):
        self.recent_orders = {}

    def prevent_duplicate(self, order):
        # Generate unique key
        key = f"{order.symbol}:{order.side}:{order.quantity}"
        timestamp = time.time()

        # Check if similar order exists within 5 seconds
        if key in self.recent_orders:
            last_time = self.recent_orders[key]
            if timestamp - last_time < 5:
                raise DuplicateOrderError("Duplicate order detected")

        self.recent_orders[key] = timestamp
        return True
```

### 4.3 Order Size Limits

| Order Type | Min Size | Max Size | Max Value | Special Rules |
|------------|----------|----------|-----------|---------------|
| **Equity Cash** | 1 share | 10% position | ₹10 lakhs | Price bands apply |
| **Equity F&O** | 1 lot | 50 lots | ₹50 lakhs | Margin required |
| **Index Options** | 1 lot | 100 lots | ₹1 crore | Greeks monitoring |
| **Commodity** | 1 lot | 10 lots | ₹25 lakhs | Special margins |

---

## 5. Strategy Safety Rules

### 5.1 Strategy Development Guidelines

```python
class SafeStrategy:
    """
    MANDATORY safety features for ALL strategies
    """
    def __init__(self):
        self.max_positions = 10
        self.position_timeout = 3600  # 1 hour max hold
        self.stop_loss_pct = 2.0
        self.daily_trade_limit = 50
        self.error_count = 0
        self.max_errors = 5

    def can_trade(self):
        """Check if strategy is allowed to trade"""
        if self.error_count >= self.max_errors:
            self.disable_strategy()
            return False

        if self.daily_trades >= self.daily_trade_limit:
            return False

        return True

    def on_error(self, error):
        """Handle errors safely"""
        self.error_count += 1
        if self.error_count >= self.max_errors:
            self.emergency_close_all_positions()
            self.disable_strategy()
```

### 5.2 Strategy Testing Requirements

| Test Type | Requirement | Pass Criteria |
|-----------|-------------|---------------|
| **Backtest** | 2+ years data | Positive Sharpe > 1 |
| **Walk Forward** | 6+ months | Consistent returns |
| **Monte Carlo** | 1000 runs | 95% profitable |
| **Stress Test** | Crash scenarios | Survives -20% day |
| **Paper Trade** | 30+ days | Matches backtest |

### 5.3 Strategy Monitoring

```yaml
real_time_monitoring:
  metrics:
    - pnl_per_trade
    - win_rate
    - risk_reward_ratio
    - max_drawdown
    - consecutive_losses

  alerts:
    - drawdown > 5%: "Warning"
    - drawdown > 10%: "Critical"
    - consecutive_losses > 3: "Review"
    - error_rate > 1%: "Investigate"

  auto_disable:
    - drawdown > 15%
    - consecutive_losses > 5
    - error_rate > 5%
```

---

## 6. System Circuit Breakers

### 6.1 Multi-Level Circuit Breakers

```python
CIRCUIT_BREAKERS = {
    "LEVEL_1": {
        "trigger": "Daily loss > 1%",
        "action": "Reduce position sizes by 50%",
        "cooldown": "1 hour",
        "auto_reset": True
    },
    "LEVEL_2": {
        "trigger": "Daily loss > 1.5%",
        "action": "Stop new positions",
        "cooldown": "2 hours",
        "auto_reset": False
    },
    "LEVEL_3": {
        "trigger": "Daily loss > 2%",
        "action": "Close all positions and halt",
        "cooldown": "24 hours",
        "auto_reset": False,
        "notification": "URGENT"
    },
    "EMERGENCY": {
        "trigger": "System error or anomaly",
        "action": "IMMEDIATE SHUTDOWN",
        "cooldown": "Manual reset only",
        "notification": "CRITICAL"
    }
}
```

### 6.2 Circuit Breaker Response Times

| Circuit Breaker | Detection Time | Action Time | Total Response |
|-----------------|----------------|-------------|----------------|
| **Daily Loss** | < 100ms | < 1 second | < 2 seconds |
| **Drawdown** | < 100ms | < 1 second | < 2 seconds |
| **Error Rate** | < 1 second | < 5 seconds | < 10 seconds |
| **System Failure** | Immediate | Immediate | < 1 second |

### 6.3 Recovery Procedures

```yaml
circuit_breaker_recovery:
  level_1:
    - Wait for cooldown
    - Auto-reset if conditions improve
    - Resume with reduced size

  level_2:
    - Manual review required
    - Check all positions
    - Verify system health
    - Manual reset by operator

  level_3:
    - Complete system audit
    - Management approval required
    - Test in paper mode first
    - Gradual restart

  emergency:
    - Full incident investigation
    - Root cause analysis
    - Fix and test
    - Senior management approval
    - Phased restart
```

---

## 7. Emergency Procedures

### 7.1 Emergency Kill Switch

```python
class EmergencyKillSwitch:
    """
    ONE-BUTTON EMERGENCY STOP
    Accessible from: Dashboard, API, CLI, Mobile
    """
    @critical_operation
    def EMERGENCY_STOP(self, reason="MANUAL"):
        # 1. Cancel ALL pending orders
        cancel_all_pending_orders()

        # 2. Close ALL positions at market
        close_all_positions_immediately()

        # 3. Disable ALL strategies
        disable_all_strategies()

        # 4. Block ALL new trades
        block_all_trading()

        # 5. Send notifications
        send_emergency_notifications(reason)

        # 6. Log everything
        log_emergency_shutdown(reason)

        return "SYSTEM HALTED - Manual intervention required"
```

### 7.2 Emergency Contact Protocol

```yaml
emergency_contacts:
  level_1_issue:
    - notify: ["operator", "tech_lead"]
    - method: ["dashboard", "email"]
    - response_time: 15 minutes

  level_2_issue:
    - notify: ["risk_manager", "head_of_trading"]
    - method: ["sms", "phone_call"]
    - response_time: 5 minutes

  critical_issue:
    - notify: ["ceo", "cto", "risk_head"]
    - method: ["phone_call", "all_channels"]
    - response_time: IMMEDIATE
```

### 7.3 Emergency Recovery Steps

1. **Immediate Actions** (< 1 minute)
   - Activate kill switch
   - Document issue
   - Notify team

2. **Assessment** (< 5 minutes)
   - Review positions
   - Calculate exposure
   - Identify root cause

3. **Mitigation** (< 15 minutes)
   - Close risky positions
   - Hedge if necessary
   - Stabilize system

4. **Recovery** (< 1 hour)
   - Fix identified issues
   - Test fixes
   - Gradual restart

---

## 8. Operational Safety

### 8.1 Daily Safety Checklist

```yaml
daily_checklist:
  pre_market:
    - [ ] Check system health
    - [ ] Verify risk limits
    - [ ] Review pending orders
    - [ ] Check margin availability
    - [ ] Verify data feeds active
    - [ ] Test kill switch

  during_market:
    - [ ] Monitor P&L real-time
    - [ ] Check circuit breakers
    - [ ] Review strategy performance
    - [ ] Watch for anomalies
    - [ ] Monitor error logs

  post_market:
    - [ ] Review day's trades
    - [ ] Check violations
    - [ ] Verify positions
    - [ ] Backup data
    - [ ] Generate reports
```

### 8.2 Monitoring Requirements

| Metric | Threshold | Alert Level | Action |
|--------|-----------|-------------|--------|
| **P&L** | -1% | Warning | Review positions |
| **Error Rate** | > 0.1% | Warning | Investigate |
| **Latency** | > 200ms | Warning | Check system |
| **CPU** | > 80% | Critical | Scale/Optimize |
| **Connection Loss** | Any | Critical | Failover |

### 8.3 Access Control

```yaml
production_access:
  traders:
    - view: all
    - trade: own_strategies_only
    - modify: own_strategies_only
    - emergency_stop: yes

  risk_managers:
    - view: all
    - trade: no
    - modify: risk_limits_only
    - emergency_stop: yes
    - override: yes

  administrators:
    - view: all
    - trade: no
    - modify: system_config
    - emergency_stop: yes
    - restart: yes
```

---

## 9. Testing Requirements

### 9.1 Mandatory Test Scenarios

```python
MANDATORY_TESTS = [
    # Market Conditions
    "Normal market day",
    "High volatility (VIX > 30)",
    "Market crash (-5% day)",
    "Flash crash recovery",
    "Circuit breaker halt",

    # System Failures
    "Network disconnection",
    "Database failure",
    "API timeout",
    "Partial fill handling",
    "Order rejection",

    # Risk Scenarios
    "Max loss reached",
    "Margin call",
    "Position limit breach",
    "Consecutive losses",

    # Edge Cases
    "Market open/close",
    "Expiry day",
    "Corporate actions",
    "Holiday schedule",
]
```

### 9.2 Performance Testing

| Test Type | Scenario | Pass Criteria |
|-----------|----------|---------------|
| **Load Test** | 1000 orders/second | < 100ms latency |
| **Stress Test** | 5000 orders/second | System stable |
| **Endurance Test** | 24 hour run | No memory leaks |
| **Spike Test** | 10x normal load | Graceful handling |
| **Failover Test** | Primary failure | < 30 second recovery |

### 9.3 Safety Testing

```yaml
safety_tests:
  order_safety:
    - duplicate_order_prevention
    - invalid_price_rejection
    - size_limit_enforcement
    - margin_verification

  risk_safety:
    - position_limit_check
    - loss_limit_trigger
    - circuit_breaker_activation
    - kill_switch_operation

  system_safety:
    - graceful_shutdown
    - data_consistency
    - audit_trail_integrity
    - recovery_procedures
```

---

## 10. Incident Response

### 10.1 Incident Classification

| Level | Description | Example | Response Time |
|-------|-------------|---------|---------------|
| **P1 - Critical** | System down, major loss | Complete failure | < 5 minutes |
| **P2 - High** | Degraded, potential loss | Slow orders | < 15 minutes |
| **P3 - Medium** | Minor issue, no loss | UI glitch | < 1 hour |
| **P4 - Low** | Cosmetic, improvement | Feature request | Next day |

### 10.2 Incident Response Plan

```yaml
incident_response:
  detect:
    - monitoring_alerts
    - user_reports
    - automated_checks

  assess:
    - severity_level
    - impact_analysis
    - root_cause

  contain:
    - isolate_issue
    - prevent_spread
    - temporary_fix

  eradicate:
    - permanent_fix
    - test_solution
    - deploy_fix

  recover:
    - restore_service
    - verify_operation
    - monitor_closely

  post_mortem:
    - document_incident
    - lessons_learned
    - preventive_measures
```

### 10.3 Post-Incident Review

```markdown
## Post-Incident Report Template

### Incident Summary
- Date/Time:
- Duration:
- Severity:
- Impact:

### Timeline
- Detection:
- Response:
- Resolution:

### Root Cause
- Technical cause:
- Process failure:
- Human factors:

### Actions Taken
- Immediate:
- Short-term:
- Long-term:

### Lessons Learned
- What went well:
- What went wrong:
- Improvements:

### Prevention
- Code changes:
- Process changes:
- Monitoring additions:
```

---

## 🔴 GOLDEN RULES - NEVER VIOLATE

1. **NEVER trade without stop-loss**
2. **NEVER bypass risk checks**
3. **NEVER ignore circuit breakers**
4. **NEVER deploy untested code to production**
5. **NEVER trade beyond your risk appetite**
6. **NEVER disable monitoring**
7. **NEVER share API keys**
8. **NEVER skip paper trading phase**
9. **NEVER increase position during drawdown**
10. **NEVER let emotions override system rules**

---

## 📞 Emergency Contacts

```yaml
internal_contacts:
  tech_support:
    phone: "+91-XXX-XXX-XXXX"
    email: "tech@trading-system.com"
    slack: "#emergency"

  risk_management:
    phone: "+91-XXX-XXX-XXXX"
    email: "risk@trading-system.com"

external_contacts:
  broker_support:
    phone: "Dhan Support Number"
    email: "support@dhan.co"

  exchange_helpdesk:
    nse: "1800-266-0050"
    bse: "1800-103-4477"
```

---

## ✅ Safety Commitment

By using this system, you acknowledge that:

1. You have read and understood all safety guidelines
2. You will follow all safety procedures
3. You accept the risks involved in trading
4. You will not hold the system creators liable for losses
5. You will regularly review and update safety measures

---

**Document Version**: 1.0.0
**Last Updated**: March 2024
**Review Frequency**: Monthly
**Next Review**: April 2024

---

⚠️ **REMEMBER: Safety is not optional. It's mandatory for survival in markets.**

---

*This document must be read and acknowledged by all system operators before gaining production access.*