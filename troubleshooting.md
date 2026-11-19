## 1. Overview
Performed a controlled Nginx service outage simulation on an Amazon Linux EC2 training instance to practise Linux incident investigation and recovery.

### Environment
- AWS EC2 running Amazon Linux
- Nginx web server
- Public subnet within a training VPC
- Amazon CloudWatch CPU monitoring

## 2. Incident Simulation
Intentionally stopped the Nginx service on the approved training instance:
```bash
sudo systemctl stop nginx
```
The EC2 instance remained running while the Nginx web service became unavailable.

## 3. Troubleshooting Commands
Check Nginx service status:
```bash
sudo systemctl status nginx
```
Test local HTTP connectivity:
```bash
curl -I --max-time 5 http://localhost
```
Check listening ports:
```bash
sudo ss -lntp | grep ':80'
```
Review recent Nginx service logs:
```bash
sudo journalctl -u nginx -n 30 --no-pager
```

## 4.Root Cause
The web service was unavailable because Nginx had been intentionally stopped for the simulation.
The EC2 instance itself remained running, demonstrating that instance availability does not guarantee application availability.

## 5.Resolution
Restarted the Nginx service:
```bash
sudo systemctl start nginx
```
Verified the service was running:
```bash
sudo systemctl is-active nginx
```
Verified HTTP connectivity:
```bash
curl -I http://localhost
```
Result: HTTP 200 OK — website successfully restored.

## 6.Monitoring Observation
The existing CloudWatch alarm monitors EC2 CPU utilization using:
- Metric: CPUUtilization
- Threshold: Greater than 70%
- Period: 5 minutes
- Evaluation: 2 out of 2 datapoints
- Alarm actions: None
This CPU alarm does not directly detect whether Nginx is running or the website is accessible.

## 7.Lessons Learned
- An EC2 instance can be healthy while an application service is unavailable.
- Linux service status and HTTP checks help identify service-level failures.
- System logs provide useful information during incident investigations.
- Service recovery should be verified after remediation.
- Infrastructure monitoring and application availability monitoring serve different purposes.

## 8.Project Scope
This exercise was performed in an approved training environment. It represents a controlled troubleshooting simulation rather than a production incident.
