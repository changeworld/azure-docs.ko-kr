---
title: Azure SQL용 Azure Active Directory의 Directory Readers 역할
description: Azure SQL 용 Azure AD의 디렉터리 판독기 역할에 대해 알아봅니다.
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/14/2020
ms.openlocfilehash: d764c6b6cff6a0ba23d659d4fda63e21aac9b155
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644189"
---
# <a name="directory-readers-role-in-azure-active-directory-for-azure-sql"></a>Azure SQL용 Azure Active Directory의 Directory Readers 역할

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

> [!NOTE]
> 이 문서의이 기능은 **공개 미리 보기로** 제공 됩니다.

Azure AD (Azure Active Directory)는 [클라우드 그룹을 사용 하 여 Azure Active Directory (미리 보기)에서 역할 할당을 관리 하](../../active-directory/roles/groups-concept.md)는 것을 소개 했습니다. 이렇게 하면 Azure AD 역할을 그룹에 할당할 수 있습니다.

Azure SQL Database, Azure SQL Managed Instance 또는 Azure Synapse Analytics에 대해 [관리 id](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) 를 사용 하도록 설정 하는 경우 azure ad [Graph API](/graph/migrate-azure-ad-graph-planning-checklist)에 대 한 읽기 액세스를 허용 하도록 azure ad [**디렉터리 구독자**](../../active-directory/roles/permissions-reference.md#directory-readers) 역할을 id에 할당 해야 합니다. SQL Database 및 Azure Synapse의 관리 되는 id를 서버 id 라고 합니다. SQL Managed Instance의 관리 되는 id를 관리 되는 인스턴스 id 라고 하며, 인스턴스를 만들 때 자동으로 할당 됩니다. SQL Database 또는 Azure Synapse에 서버 id를 할당 하는 방법에 대 한 자세한 내용은 [서비스 주체를 사용 하 여 AZURE AD 사용자 만들기를](authentication-aad-service-principal.md#enable-service-principals-to-create-azure-ad-users)참조 하세요.

**디렉터리 판독기** 역할은 다음 작업을 수행 하는 데 필요 합니다.

- SQL Managed Instance에 대 한 Azure AD 로그인 만들기
- Azure SQL에서 Azure AD 사용자를 가장 합니다.
- [ALTER USER (transact-sql)](/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-mi-current&preserve-view=true#d-map-the-user-in-the-database-to-an-azure-ad-login-after-migration) 명령을 사용 하 여 Windows 인증을 사용 하는 사용자 SQL SERVER Azure AD 인증을 사용 하 여 SQL Managed Instance로 마이그레이션
- SQL Managed Instance에 대 한 Azure AD 관리자 변경
- [서비스 사용자 (응용 프로그램)](authentication-aad-service-principal.md) 가 azure SQL에서 azure AD 사용자를 만들 수 있도록 허용

## <a name="assigning-the-directory-readers-role"></a>디렉터리 판독기 역할 할당

[**디렉터리 판독기**](../../active-directory/roles/permissions-reference.md#directory-readers) 역할을 id에 할당 하려면 [전역 관리자](../../active-directory/roles/permissions-reference.md#global-administrator) 또는 [권한 있는 역할 관리자](../../active-directory/roles/permissions-reference.md#privileged-role-administrator) 권한이 있는 사용자가 필요 합니다. SQL Database, SQL Managed Instance 또는 Azure Synapse를 종종 관리 하거나 배포 하는 사용자는 이러한 높은 권한 있는 역할에 액세스할 수 없습니다. 이로 인해 사용자가 계획 되지 않은 Azure SQL 리소스를 만들거나 종종 큰 조직에서 액세스할 수 없는 높은 권한이 있는 역할 멤버의 도움을 받아야 하는 경우가 종종 있습니다.

SQL Managed Instance의 경우 [관리형 인스턴스에 대한 Azure AD 관리자를 설정](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)하려면 먼저 관리형 인스턴스 ID에 **디렉터리 읽기 권한자** 역할을 할당해야 합니다. 

논리 서버에 대한 Azure AD 관리자를 설정할 때에는 SQL Database 또는 Azure Synapse에서 서버 ID에 **디렉터리 읽기 권한자** 역할을 할당할 필요가 없습니다. 그러나 Azure AD 애플리케이션 대신 SQL Database 또는 Azure Synapse에서 Azure AD 개체 만들기를 사용하려면 **디렉터리 읽기 권한자** 역할이 필요합니다. SQL 논리 서버 ID에 역할이 할당되지 않으면 Azure SQL에서 Azure AD 사용자를 만들 수 없습니다. 자세한 내용은 [Azure SQL을 사용하는 Azure Active Directory 서비스 주체](authentication-aad-service-principal.md)를 참조하세요.

## <a name="granting-the-directory-readers-role-to-an-azure-ad-group"></a>Azure AD 그룹에 디렉터리 판독기 역할 부여

현재 **공개 미리 보기로** 제공 되는 [전역 관리자](../../active-directory/roles/permissions-reference.md#global-administrator) 또는 [권한 있는 역할 관리자](../../active-directory/roles/permissions-reference.md#privileged-role-administrator) 가 Azure AD 그룹을 만들고 해당 그룹에 대 한 [**디렉터리 독자**](../../active-directory/roles/permissions-reference.md#directory-readers) 권한을 할당할 수 있습니다. 이렇게 하면이 그룹의 구성원에 대 한 Azure AD Graph API에 액세스할 수 있습니다. 또한이 그룹의 소유자 인 Azure AD 사용자는 Azure SQL 논리 서버의 id를 포함 하 여이 그룹의 새 구성원을 할당할 수 있습니다.

이 솔루션에는 그룹을 만들고 사용자를 일회성 작업으로 할당 하기 위해 높은 권한 사용자 (전역 관리자 또는 권한 있는 역할 관리자)가 필요 하지만 Azure AD 그룹 소유자는 향후 추가 구성원을 할당할 수 있습니다. 이렇게 하면 나중에 Azure AD 테 넌 트에서 모든 SQL 데이터베이스, SQL 관리 되는 인스턴스 또는 Azure Synapse 서버를 구성할 수 있는 높은 권한 사용자를 포함할 필요가 없습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: Azure AD 그룹에 디렉터리 읽기 권한자 역할 할당 및 역할 할당 관리](authentication-aad-directory-readers-role-tutorial.md)