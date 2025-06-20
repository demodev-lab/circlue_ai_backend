# Django REST Framework

## uv로 가상환경 생성

```shell
uv init
uv python pin 3.13
uv venv
source .venv/bin/activate
```

## Django REST Framework 패키지 설치

```shell
uv add django djangorestframework markdown django-filter pygments
```

## Django 프로젝트 생성

```shell
uv run django-admin startproject config .
```

## Django App 생성

```shell
uv run django-admin startapp api
```

## Django 프로젝트 실행

```shell
uv run python manage.py runserver
```

<details>
<summary>## API 문서화 - drf-spectacular (추천)</summary>

### 설치

```shell
uv add drf-spectacular
```

### settings.py 설정

```python
INSTALLED_APPS = [
    # ... 기타 앱들
    'rest_framework',
    'drf_spectacular',  # API 문서화
]

REST_FRAMEWORK = {
    # ... 기타 설정들
    'DEFAULT_SCHEMA_CLASS': 'drf_spectacular.openapi.AutoSchema',
}

# API 문서화 설정
SPECTACULAR_SETTINGS = {
    'TITLE': 'Your API',
    'DESCRIPTION': 'Your project description',
    'VERSION': '1.0.0',
    'SERVE_INCLUDE_SCHEMA': False,
    # Optional UI 설정
    'SWAGGER_UI_SETTINGS': {
        'deepLinking': True,
        'displayOperationId': True,
        'defaultModelsExpandDepth': 1,
        'defaultModelExpandDepth': 1,
    },
}
```

### urls.py 설정

```python
from django.contrib import admin
from django.urls import path, include
from drf_spectacular.views import (
    SpectacularAPIView,
    SpectacularSwaggerView,
    SpectacularRedocView
)

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include('your_app.urls')),  # 기본 API 경로

    # API 문서화 경로
    path('api/schema/', SpectacularAPIView.as_view(), name='schema'),
    path('api/docs/', SpectacularSwaggerView.as_view(url_name='schema'), name='swagger-ui'),
    path('api/redoc/', SpectacularRedocView.as_view(url_name='schema'), name='redoc'),
]
```

### 사용법

1. **Swagger UI**: `http://localhost:8000/api/docs/`
2. **ReDoc**: `http://localhost:8000/api/redoc/`
3. **스키마 파일**: `http://localhost:8000/api/schema/`

### 고급 설정 (선택사항)

```python
# views.py에서 상세한 문서화
from drf_spectacular.utils import extend_schema, OpenApiResponse

@extend_schema(
    summary="사용자 목록 조회",
    description="등록된 모든 사용자의 목록을 반환합니다.",
    responses={
        200: OpenApiResponse(description="성공"),
        401: OpenApiResponse(description="인증 실패"),
    }
)
class UserListView(generics.ListAPIView):
    queryset = User.objects.all()
    serializer_class = UserSerializer
```

### 명령어로 스키마 생성

```shell
python manage.py spectacular --file schema.yml
```

</details>
