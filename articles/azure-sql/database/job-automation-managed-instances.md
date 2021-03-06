---
title: Azure SQL Managed Instance의 SQL 에이전트 작업을 사용한 작업 자동화
description: Azure SQL Managed Instance에서 Transact-sql (T-sql) 스크립트를 실행 하는 자동화 옵션
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=1
dev_langs:
- TSQL
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 02/01/2021
ms.openlocfilehash: 3be01c304a40317e0d21baf6789ef1376cd89b80
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608081"
---
# <a name="automate-management-tasks-using-sql-agent-jobs-in-azure-sql-managed-instance"></a>Azure SQL Managed Instance에서 SQL 에이전트 작업을 사용 하 여 관리 작업 자동화
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

SQL Server 및 [SQL Managed Instance](../../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)에서 [SQL Server 에이전트](/sql/ssms/agent/sql-server-agent) 를 사용 하 여 transact-sql (t-sql) 쿼리를 실행 하 고 유지 관리 작업을 수행 하기 위해 하나 이상의 데이터베이스에 대해 정기적으로 실행할 수 있는 작업을 만들고 예약할 수 있습니다. 이 문서에서는 sql Managed Instance 용 SQL 에이전트를 소개 했습니다.

> [!Note]
> SQL 에이전트는 Azure SQL Database 또는 Azure Synapse Analytics에서 사용할 수 없습니다. 대신 [탄력적 작업을 사용 하 여 작업을 자동화](job-automation-overview.md)하는 것이 좋습니다.

### <a name="sql-agent-job-limitations-in-azure-sql-managed-instance"></a>Azure SQL 관리 되는 인스턴스의 SQL 에이전트 작업 제한 사항

SQL Server에서 사용할 수 있는 SQL 에이전트와 SQL Managed Instance의 차이점에 대해 주목 해야 합니다. SQL Server와 SQL Managed Instance의 지원 되는 기능 차이점에 대 한 자세한 내용은 [AZURE sql Managed Instance t-sql 차이점 SQL Server](../../azure-sql/managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)을 참조 하세요. 

SQL Server에서 사용할 수 있는 SQL 에이전트의 일부 기능은 SQL Managed Instance에서 지원 되지 않습니다.

- SQL 에이전트 설정은 읽기 전용입니다. 
    - 시스템 저장 프로시저는 `sp_set_agent_properties` SQL Managed Instance에서 지원 되지 않습니다.
- Sql 에이전트를 사용 하거나 사용 하지 않도록 설정 하는 기능은 현재 SQL Managed Instance에서 지원 되지 않습니다. SQL 에이전트는 항상 실행됩니다.
- 알림은 부분적으로 지원 됩니다.
  - 호출기는 지원되지 않습니다.
  - NetSend는 지원되지 않습니다.
  - 경고는 지원되지 않습니다.
- 프록시는 지원되지 않습니다.
- Eventlog는 지원되지 않습니다.
- 유휴 CPU를 기반으로 하는 작업 일정 트리거는 지원 되지 않습니다.

## <a name="when-to-use-sql-agent-jobs"></a>SQL 에이전트 작업을 사용 하는 경우 

SQL 에이전트 작업을 사용할 수 있는 몇 가지 시나리오는 다음과 같습니다.

- 관리 작업을 자동화한 다음, 주중 매일, 일정 시간 후 등에 실행되도록 예약합니다.
  - 스키마 변경, 자격 증명 관리, 성능 데이터 수집 또는 테넌트(고객) 원격 분석 수집을 배포합니다.
  - 참조 데이터(모든 데이터베이스에서 공통적인 정보)를 업데이트하고 Azure Blob Storage에서 데이터를 로드합니다.
  - DBCC CHECKDB를 비롯 한 일반적인 유지 관리 작업을 통해 데이터 무결성 또는 인덱스 유지 관리를 통해 쿼리 성능을 향상 시킬 수 있습니다. 사용량이 적은 시간 중과 같이 데이터베이스 컬렉션에 대해 되풀이해서 실행하려면 작업을 구성합니다.
  - 지속적으로 데이터베이스 집합에서 중앙 테이블로 쿼리 결과 수집 지속적으로 성능 쿼리를 실행하고 실행할 추가 작업을 트리거하도록 구성할 수 있습니다.
- 보고에 대한 데이터 수집
  - 데이터베이스의 컬렉션에서 단일 대상 테이블로 데이터를 집계합니다.
  - 큰 데이터베이스 집합에 대해 더 오래 실행되는 데이터 처리 쿼리(예: 고객 원격 분석 수집) 실행. 추가 분석을 위해 결과가 단일 대상 테이블에 수집됩니다.
- 데이터 이동
  - 데이터베이스에서 변경된 내용을 다른 데이터베이스로 복제하거나 원격 데이터베이스에서 만들어진 업데이트를 수집하고 변경된 내용을 데이터베이스에 적용하는 작업을 만듭니다.
  - SSIS(SQL Server Integration Services)를 사용하여 데이터베이스에서 데이터를 로드하는 작업을 만듭니다.

## <a name="sql-agent-jobs-in-azure-sql-managed-instance"></a>Azure SQL 관리 되는 인스턴스의 SQL 에이전트 작업

Sql 에이전트 작업은 sql 에이전트 서비스에서 실행 되며,이 서비스는 SQL Server 및 SQL Managed Instance에서 태스크 자동화에 계속 사용 됩니다. 

SQL 에이전트 작업은 데이터베이스에 대해 지정된 일련의 T-SQL 스크립트입니다. 작업을 사용하여 두 번 이상 실행할 수 있으며 성공 또는 실패를 모니터링하는 관리 작업을 정의합니다. 

하나의 로컬 서버 또는 여러 원격 서버에서 작업을 실행할 수 있습니다. SQL 에이전트 작업은 SQL Managed Instance 서비스 내에서 실행 되는 내부 데이터베이스 엔진 구성 요소입니다.

SQL 에이전트 작업에는 몇 가지 주요 개념이 있습니다.

- **작업 단계** 는 작업 내에서 실행되어야 하는 하나 이상의 단계로 설정됩니다. 모든 작업 단계에서 작업 단계가 성공 또는 실패인 경우에 발생되어야 하는 다시 시도 전략 및 작업을 정의할 수 있습니다.
- **일정** 은 작업을 실행해야 하는 시기를 정의합니다.
- **알림** 을 통해 작업이 완료되면 이메일을 통해 운영자에게 알리는 데 사용할 규칙을 정의할 수 있습니다.

### <a name="sql-agent-job-steps"></a>SQL 에이전트 작업 단계

SQL 에이전트 작업 단계는 SQL 에이전트가 실행해야 하는 작업의 시퀀스입니다. 모든 단계에는 단계가 성공하거나 실패하는 경우 실행되어야 하는 다음 단계와 함께 실패 시 재시도 횟수가 있습니다.

SQL 에이전트를 사용 하면 데이터베이스에 대해 단일 Transact-sql 일괄 처리를 실행 하는 Transact-sql 작업 단계, SSIS 런타임을 사용 하 여 데이터를 로드 하는 데 사용할 수 있는 [ssis 작업 단계](../../data-factory/how-to-invoke-ssis-package-managed-instance-agent.md) , 데이터베이스의 변경 내용을 다른 데이터베이스로 게시할 수 있는 [복제](../managed-instance/replication-transactional-overview.md) 단계 등 다양 한 유형의 작업 단계를 만들 수 있습니다.

> [!Note]
> Azure SQL Managed Instance에서 호스트 하는 SSISDB를 사용 하 여 Azure SSIS Integration Runtime를 활용 하는 방법에 대 한 자세한 내용은 [Azure Data Factory에서 AZURE sql Managed Instance SQL Server Integration Services (SSIS) 사용](../../data-factory/how-to-use-sql-managed-instance-with-ir.md)을 참조 하세요.

[트랜잭션 복제](../managed-instance/replication-transactional-overview.md) 는 테이블의 변경 내용을 Azure SQL Managed Instance, Azure SQL Database 또는 SQL Server의 다른 데이터베이스로 복제할 수 있습니다. 자세한 내용은 [AZURE SQL Managed Instance에서 복제 구성](../../azure-sql/managed-instance/replication-between-two-instances-configure-tutorial.md)을 참조 하세요. 

다음을 비롯 한 다른 유형의 작업 단계는 현재 SQL Managed Instance 지원 되지 않습니다.

- 병합 복제 작업 단계는 지원되지 않습니다.
- 큐 판독기는 지원되지 않습니다.
- Analysis Services는 지원되지 않습니다.
 
### <a name="sql-agent-job-schedules"></a>SQL 에이전트 작업 일정

일정은 작업이 실행되는 시기를 지정합니다. 동일한 일정에 따라 둘 이상의 작업을 실행할 수 있으며, 둘 이상의 일정을 동일한 작업에 적용할 수 있습니다.

일정은 작업이 실행되는 시기에 대해 다음 조건을 정의할 수 있습니다.

- SQL Server 에이전트가 시작될 때마다 작업은 모든 장애 조치(failover) 후 활성화됩니다.
- 특정 날짜 및 시간에 한 번 - 일부 작업의 지연된 실행에 유용합니다.
- 되풀이 일정.

> [!Note]
> SQL Managed Instance는 현재 CPU가 유휴 상태일 때 작업을 시작할 수 없습니다.

### <a name="sql-agent-job-notifications"></a>SQL 에이전트 작업 알림

SQL 에이전트 작업을 통해 작업이 성공적으로 완료되거나 실패할 때 알림을 받을 수 있습니다. 이메일을 통해 알림을 받을 수 있습니다.

아직 사용 하도록 설정 되지 않은 경우 먼저 Azure SQL Managed Instance에서 [데이터베이스 메일 기능](/sql/relational-databases/database-mail/database-mail) 을 구성 해야 합니다.

```sql
GO
EXEC sp_configure 'show advanced options', 1;
GO
RECONFIGURE;
GO
EXEC sp_configure 'Database Mail XPs', 1;
GO
RECONFIGURE
```

예를 들어 전자 메일 알림을 보내는 데 사용 되는 전자 메일 계정을 설정 합니다. 계정을 이라는 전자 메일 프로필에 할당 `AzureManagedInstance_dbmail_profile` 합니다. SQL Managed Instance에서 SQL 에이전트 작업을 사용 하 여 전자 메일을 보내려면 호출 해야 하는 프로필이 있어야 합니다 `AzureManagedInstance_dbmail_profile` . 그렇지 않으면 sql Managed Instance는 SQL 에이전트를 통해 전자 메일을 보낼 수 없습니다. 다음 샘플을 참조 하세요.

```sql
-- Create a Database Mail account
EXECUTE msdb.dbo.sysmail_add_account_sp
    @account_name = 'SQL Agent Account',
    @description = 'Mail account for Azure SQL Managed Instance SQL Agent system.',
    @email_address = '$(loginEmail)',
    @display_name = 'SQL Agent Account',
    @mailserver_name = '$(mailserver)' ,
    @username = '$(loginEmail)' ,
    @password = '$(password)';

-- Create a Database Mail profile
EXECUTE msdb.dbo.sysmail_add_profile_sp
    @profile_name = 'AzureManagedInstance_dbmail_profile',
    @description = 'E-mail profile used for messages sent by Managed Instance SQL Agent.';

-- Add the account to the profile
EXECUTE msdb.dbo.sysmail_add_profileaccount_sp
    @profile_name = 'AzureManagedInstance_dbmail_profile',
    @account_name = 'SQL Agent Account',
    @sequence_number = 1;
```

[Sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) 시스템 저장 프로시저를 사용 하 여 t-sql을 통해 데이터베이스 메일 구성을 테스트 합니다.

```sql
DECLARE @body VARCHAR(4000) = 'The email is sent from ' + @@SERVERNAME;
EXEC msdb.dbo.sp_send_dbmail  
    @profile_name = 'AzureManagedInstance_dbmail_profile',  
    @recipients = 'ADD YOUR EMAIL HERE',  
    @body = 'Add some text',  
    @subject = 'Azure SQL Instance - test email';  
```

SQL 에이전트 작업에 문제가 발생했음을 운영자에게 알릴 수 있습니다. 운영자는 SQL Managed Instance의 하나 이상의 인스턴스에 대한 유지 관리를 담당하는 개인에 대한 연락처 정보를 정의합니다. 운영자 책임이 개인 한 명에게 할당되는 경우도 있습니다.

SQL Managed Instance 또는 SQL Server의 여러 인스턴스를 사용하는 시스템에서는 여러 개인이 운영자 책임을 공유할 수 있습니다. 운영자는 보안 정보를 포함하지 않으며 보안 주체를 정의하지 않습니다. 가장 이상적으로 운영자는 책임이 변경 될 수 있는 개인은 아니지만 전자 메일 배포 그룹입니다.   

다음 예제에 나와 있는 Transact-sql 스크립트 또는 SQL Server Management Studio (SSMS)를 사용 하 여 [운영자를 만들](/sql/relational-databases/system-stored-procedures/sp-add-operator-transact-sql) 수 있습니다.

```sql
EXEC msdb.dbo.sp_add_operator
    @name=N'AzureSQLTeam',
    @enabled=1,
    @email_address=N'AzureSQLTeamn@contoso.com';
```

SSMS의 [데이터베이스 메일 로그](/sql/relational-databases/database-mail/database-mail-log-and-audits) 를 통해 전자 메일의 성공 또는 실패를 확인 합니다.

그러면 [모든 SQL 에이전트 작업을 수정](/sql/relational-databases/system-stored-procedures/sp-update-job-transact-sql) 하 고, SSMS 또는 다음 transact-sql 스크립트를 사용 하 여 작업이 완료, 실패 또는 성공 하는 경우 전자 메일을 통해 알림이 표시 되는 운영자를 할당할 수 있습니다.

```sql
EXEC msdb.dbo.sp_update_job @job_name=N'Load data using SSIS',
    @notify_level_email=3, -- Options are: 1 on succeed, 2 on failure, 3 on complete
    @notify_email_operator_name=N'AzureSQLTeam';
```

### <a name="sql-agent-job-history"></a>SQL 에이전트 작업 기록

현재 Azure SQL Managed Instance는 기본 레지스트리 값에 저장 되기 때문에 SQL 에이전트 속성을 변경할 수 없습니다. 즉, 작업 기록 레코드에 대 한 에이전트 보존 정책 조정을 위한 옵션은 기본적으로 1000 개의 총 레코드와 작업당 최대 100 기록 레코드 수로 고정 됩니다.

### <a name="sql-agent-fixed-database-role-membership"></a>SQL 에이전트 고정 데이터베이스 역할 멤버 자격

Sysadmin 로그인에 연결 된 사용자가 msdb 시스템 데이터베이스에 있는 세 개의 SQL 에이전트 고정 데이터베이스 역할에 추가 되 면 이러한 로그인이 작동 하기 위해 마스터 저장 프로시저에 명시적 실행 권한을 부여 해야 하는 문제가 있습니다. 이런 문제가 발생하면 "개체 <object_name>에 대한 EXECUTE 권한이 거부되었습니다(Microsoft SQL Server, 오류: 229)" 오류 메시지가 표시됩니다. 

Msdb의 SQL 에이전트 고정 데이터베이스 역할 (SQLAgentUserRole, SQLAgentReaderRole 또는 SQLAgentOperatorRole)에 사용자를 추가한 후 이러한 역할에 추가 된 각 사용자 로그인에 대해 아래 T-sql 스크립트를 실행 하 여 나열 된 시스템 저장 프로시저에 대 한 실행 권한을 명시적으로 부여 합니다. 이 예에서는 사용자 이름과 로그인 이름이 동일 하다 고 가정 합니다. 

```sql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name];
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name];
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name];
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name];
```

## <a name="learn-more"></a>자세한 정보

- [Azure SQL Managed Instance란?](../managed-instance/sql-managed-instance-paas-overview.md)
- [SQL Managed Instance & Azure SQL Database의 새로운 기능](../../azure-sql/database/doc-changes-updates-release-notes.md?tabs=managed-instance)
- [SQL Server의 Azure SQL Managed Instance T-sql 차이점](../../azure-sql/managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)
- [기능 비교: Azure SQL Database 및 Azure SQL Managed Instance](../../azure-sql/database/features-comparison.md)
