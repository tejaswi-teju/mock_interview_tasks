pipeline {
    agent any

    environment {
        CPU_THRESHOLD = '0'
        MEMORY_THRESHOLD = '0'
        UPTIME_THRESHOLD = '0'
    }

    stages {
        stage ("Shell script to calculate system health checks") {
            steps {
                sh '''

                # Calculating CPU Usage
                CPU_IDLE=$(top -bn1 | grep 'Cpu(s)' | sed 's/.*, *\\([0-9.]*\\)%* id.*/\\1/' | awk '{print $1}')
                CPU_USAGE=$(printf "%.0f" $(echo "100 - $CPU_IDLE" | bc))

                # Calculation Memory Usage
                MEMORY_USAGE=$(free | awk '/Mem/{printf "%.0f", $3/$2 * 100}')

                # Calculating System Uptime
                SYSTEM_UPTIME=$(cat /proc/uptime | awk '{printf "%.0f", $1/3600}')

                echo "----------------------------------------------------------------"
                echo "Health checks based on CPU, memory utilization and System uptime"
                echo "-----------------------------------------------------------------"

                ALERT_MSG=""
                
                if ["$CPU_USAGE" -gt "$CPU_THRESHOLD"]; then
                    ALERT_MSG+="Cpu usage alert. Please check the system"\n
                fi

                if ["$MOMERY_USAGE" -gt "$MEMORY_THRESHOLD"]; then
                    ALERT_MSG+="MEmory usage alert. Please check the system"\n
                fi 

                if ["$SYSTEM_UPTIME" -gt "$UPTIME_THRESHOLD"]; then
                    ALERT_MSG+="System Uptime alert. Please check the system"\n
                fi 

                if [ -z "$ALERT_MSG" ]; then
                    echo "System Health is good"
                    ALERT_MSG="System Health is good"
                fi
                    
                echo -e "$ALERT_MSG" > health_checks.txt

                '''
            }
        }

        stage ("E-mail Notification") {
            steps {
                emailext (
                    to : "tejaswiteju211@gmail.com",
                    subject : "Health check reports",
                    body : "${readFile('health_checks.txt')}"
                )

            }
        }
    }
}
