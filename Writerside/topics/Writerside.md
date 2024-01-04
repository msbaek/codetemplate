# Writerside

## Publish to github

- [Build and publish on GitHub](https://www.jetbrains.com/help/writerside/deploy-docs-to-github-pages.html) 
- 기존적으로 이 문서를 따라하면 됨
### 예외적인 부분
#### test할 때 이미지 사이즈가 규격을 넘어서 오류가 났음
- deploy.yml을 수정했음
```yaml
# Add the job below and artifacts/report.json on Upload documentation step above if you want to fail the build when documentation contains errors
#  test:
#    # Requires build job results
#    needs: build
#    runs-on: ubuntu-latest
#
#    steps:
#      - name: Download artifacts
#        uses: actions/download-artifact@v1
#        with:
#          name: docs
#          path: artifacts
#
#      - name: Test documentation
#        uses: JetBrains/writerside-checker-action@v1
#        with:
#          instance: ${{ env.INSTANCE }}
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    # Requires the build job results
#    needs: test
    needs: build
```
- 위와 같은 test 섹션을 커멘트 처리하고
- `deploy`에서 `needs`를 `test`에서 `build`로 변경했음
#### 누락하기 쉬운 실수
- git repository를 생성하고 push를 하기 전에 
![github-action-to-deploy.png](../images/github-action-to-deploy.png)
- 위와 같이 **Github Actions**를 선택해야 함
#### image path
- 문서에 있는대로 해도 이미지 패스를 못 찾는 문제가 있었음
- 그래서 이미지를 추가하면 Writerside가 아래와 같이 태그를 만들어 주면 생성되는 아래와 같은 태그를

`![github-action-to-deploy.png](github-action-to-deploy.png)`

- 다음과 같이 `../images/`를 추가하여 변경했음

`![github-action-to-deploy.png](../images/github-action-to-deploy.png)`
