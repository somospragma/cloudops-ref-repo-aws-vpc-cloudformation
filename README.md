# cloudops-ref-repo-aws-vpc-cloudformation

## Description
Este template de CloudFormation permite desplegar una VPC en AWS con configuraciones de DNS activadas y etiquetado corporativo completo, siguiendo las convenciones de nomenclatura y mejores prácticas de governance de la organización.  
El objetivo es estandarizar la creación de VPCs, garantizando compliance con las políticas de seguridad y gestión de costos, así como la correcta identificación de recursos en entornos **QA, Dev, Preproducción y Producción**.

## Architecture
┌─────────────────────────────────────────────┐
│ AWS Account │
│ ┌─────────────────────────────────────────┐ │
│ │ VPC │ │
│ │ • CIDR: definido por parámetro │ │
│ │ • DNS Support: habilitado │ │
│ │ • DNS Hostnames: habilitado │ │
│ │ • Tenancy: default │ │
│ │ • Tags: governance completo │ │
│ └─────────────────────────────────────────┘ │
└─────────────────────────────────────────────┘


## Usage
### Despliegue Básico
```bash
aws cloudformation create-stack \
  --stack-name cliente-proyecto-dev-vpc \
  --template-body file://vpc.yml \
  --parameters \
    ParameterKey=pVpcCidr,ParameterValue=10.0.0.0/16 \
    ParameterKey=pCustomerName,ParameterValue=Pragma \
    ParameterKey=pProjectName,ParameterValue=VulcanoProject \
    ParameterKey=pEnviroment,ParameterValue=dev \
    ParameterKey=pServiceType,ParameterValue=Networking \
    ParameterKey=pAplication,ParameterValue=CoreNetwork \
    ParameterKey=pCostCenter,ParameterValue=9904 \
    ParameterKey=pOwner,ParameterValue=DevOps.Team \
    ParameterKey=pArea,ParameterValue=TI

### Usando Archivo de Parámetros
aws cloudformation create-stack \
  --stack-name cliente-proyecto-dev-vpc \
  --template-body file://vpc.yml \
  --parameters file://sample/vpc-parameters.json

### Ejemplos en entorno de producción
{
  "Parameters": [
    { "ParameterKey": "pVpcCidr", "ParameterValue": "172.16.0.0/16" },
    { "ParameterKey": "pCustomerName", "ParameterValue": "Pragma" },
    { "ParameterKey": "pProjectName", "ParameterValue": "VulcanoProject" },
    { "ParameterKey": "pEnviroment", "ParameterValue": "pdn" },
    { "ParameterKey": "pServiceType", "ParameterValue": "Networking" },
    { "ParameterKey": "pAplication", "ParameterValue": "CoreNetwork" },
    { "ParameterKey": "pCostCenter", "ParameterValue": "6621" },
    { "ParameterKey": "pOwner", "ParameterValue": "Networking.Team" },
    { "ParameterKey": "pArea", "ParameterValue": "Operaciones" }
  ]
}

## Requirements
|       NAME      |                     VERSION                        |
| --------------- | -------------------------------------------------- |
| aws-cli         | >= 2.0                                             |
| cloudformation  | >= 2010-09-09                                      |
| AWS Account     | Cuenta AWS activa                                  |
| IAM Permissions | Permisos para `ec2:CreateVpc` y `ec2:DescribeVpcs` |

## Templates
|  NAME   |           TYPE          |
| ------- | ----------------------- |
| vpc.yml | CloudFormation Template |

## Resources
|      NAME     |   TYPE   |
| ------------- | -------- |
| AWS::EC2::VPC | resource |

## Inputs
|     NAME      |       DESCRIPTION       |  TYPE  | REQUIRED |      ALLOWED VALUES      |
| ------------- | ----------------------- | ------ | -------- | ------------------------ |
| pVpcCidr      | Rango CIDR de la VPC    | String | yes      | CIDR válido              |
| pCustomerName | Nombre del cliente      | String | yes      | Cualquier string válido  |
| pProjectName  | Nombre del proyecto     | String | yes      | Cualquier string válido  |
| pEnviroment   | Ambiente del despliegue | String | yes      | qa, dev, pdn, prepdn     |
| pServiceType  | Tipo de servicio        | String | yes      | Cualquier string válido  |
| pAplication   | Aplicación o servicio   | String | yes      | Cualquier string válido  |
| pCostCenter   | Centro de costos        | Number | yes      | 9904, 6621, 1222         |
| pOwner        | Responsable del recurso | String | yes      | Formato: Nombre.Apellido |
| pArea         | Área organizacional     | String | yes      | Cualquier string válido  |

## Outputs
| NAME   | DESCRIPTION                                         |
| ------ | --------------------------------------------------- |
| oVPCId | ID de la VPC creada para referencia en otros stacks |

## Validation
### Pre-Deployment Validation
aws cloudformation validate-template --template-body file://vpc.yml

### Post-Deployment Validation
aws cloudformation describe-stacks --stack-name cliente-proyecto-dev-vpc
aws ec2 describe-vpcs --vpc-ids vpc-xxxxxxxxxxxxxxxxx

## Security
### Controles de Seguridad Implementados
| CONTROL                | DESCRIPTION                                       | ESTADO |
| ---------------------- | ------------------------------------------------- | ------ |
| Etiquetado Obligatorio | Todos los recursos incluyen tags corporativos     | ✅      |
| Parámetros Validados   | Valores restringidos para ambientes y costos      | ✅      |
| DNS Habilitado         | Soporte para DNS y hostnames activado por defecto | ✅      |

### Recomendaciones de Seguridad
1. Utilizar rangos CIDR que no se superpongan con otras redes internas.
2. Evitar exponer recursos de subnets públicas sin controles adicionales.
3. Mantener consistencia en el etiquetado para trazabilidad de costos.

## Troubleshooting
| ERROR                                | CAUSA                      | SOLUCIÓN                                                  |
| ------------------------------------ | -------------------------- | --------------------------------------------------------- |
| The CIDR 'X.X.X.X/XX' is invalid     | Formato CIDR incorrecto    | Usar formato válido IPv4                                  |
| You are not authorized to perform... | Permisos IAM insuficientes | Verificar políticas asignadas                             |
| VPC limit exceeded                   | Límite de VPCs alcanzado   | Solicitar aumento a AWS Support o eliminar VPCs no usadas |

## Cleanup
aws cloudformation delete-stack --stack-name cliente-proyecto-dev-vpc














