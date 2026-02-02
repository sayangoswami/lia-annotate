<!--
author: Sayan Goswami
version: 0.1
language: en
comment: Full-slide SVG annotation overlay for live teaching

@test: @test_(@uid)

@test_
<div id="test_@0" style="background: yellow; padding: 2em;">
  TEST MACRO RENDERED

  <script style="display:block">
    console.log("TEST SCRIPT EXECUTED", "@0");
  </script>
</div>
@end

-->

