# Copyright (c) Aemon Cannon. All rights reserved.
# The use and distribution terms for this software are covered by the
# Common Public License 1.0 (http://opensource.org/licenses/cpl.php)
# which can be found in the file CPL.TXT at the root of this distribution.
# By using this software in any fashion, you are agreeing to be bound by
# the terms of this license.
# You must not remove this notice, or any other, from this software.


require 'rexml/document'
include REXML

$debug = true

MXMLC = PLATFORM =~ /win/ ? "mxmlc.exe" : "~/lib/flex3/bin/mxmlc"
COMPC = PLATFORM =~ /win/ ? "compc.exe" : "~/lib/flex3/bin/compc"
DEBUG_PROJECTOR = PLATFORM =~ /win/ ? "sa_flashplayer_9_debug.exe" : "~/bin/flashplayer_debug_projector"

SHARED_CLASS_PATH = [
                     "src/as3",
                     File.expand_path("~/lib/flexunit/trunk/FlexUnitLib/src")
                    ]

COMPILE_OPTIONS = [
                   "+configname=flex",
                   "-default-frame-rate=60",
                   "-compiler.warn-no-type-decl=false",
                   "-compiler.optimize=true",
                   # ABCDump has tons of warnings unless we disable these:
                   "-compiler.source-path #{SHARED_CLASS_PATH.join(" ")}"
                  ]

SWC_OPTIONS = [
               "-include-classes com.las3r.repl.App",
               "-directory=false",
               "-debug=false",
               "-compiler.warn-no-type-decl=false",
               "-compiler.optimize=true",
               "-source-path #{SHARED_CLASS_PATH.join(" ")}"
              ]


SHARED_SOURCES = FileList["./src/as3/**/*"]
LAS3R_STDLIB = FileList["./src/lsr/**/*"]
THIS_RAKEFILE = FileList["./Rakefile"]
DEMO_SWF_ENTRY_POINTS = FileList["src/as3/com/las3r/test/demos/*.as"]
DEMO_SWF_TARGETS = DEMO_SWF_ENTRY_POINTS.collect{|ea| "./bin/" + File.basename(ea, ".as") + ".swf" }

UNIT_TEST_RUNNER_TARGET = "./bin/unit_test_runner.swf"
file UNIT_TEST_RUNNER_TARGET => SHARED_SOURCES + LAS3R_STDLIB do
  options = COMPILE_OPTIONS + [$debug ? "-compiler.debug=true" : "", "-default-size 1000 600"]
  sh "#{MXMLC} #{options.join(" ")} -file-specs src/as3/com/las3r/test/FlexUnitTestRunner.mxml -output=#{UNIT_TEST_RUNNER_TARGET}"
end

REPL_TARGET = "./bin/repl.swf"
file REPL_TARGET => SHARED_SOURCES + LAS3R_STDLIB do
  options = COMPILE_OPTIONS + [$debug ? "-compiler.debug=true": "", "-default-size 635 450"]
  sh "#{MXMLC} #{options.join(" ")} -file-specs src/as3/com/las3r/repl/App.as -output=#{REPL_TARGET}"
end

SWC_TARGET = "./bin/las3r.swc"
file SWC_TARGET => SHARED_SOURCES + LAS3R_STDLIB do
  sh "#{COMPC} #{SWC_OPTIONS.join(" ")} -output=#{SWC_TARGET}"
end

LISP_UNITS_TARGET = "./bin/lisp_units.swf"
file LISP_UNITS_TARGET => SHARED_SOURCES + LAS3R_STDLIB do
  options = COMPILE_OPTIONS + [$debug ? "-compiler.debug=true": "", "-default-size 635 450"]
  sh "#{MXMLC} #{options.join(" ")} -file-specs src/as3/com/las3r/repl/LispTestRunner.as -output=#{LISP_UNITS_TARGET}"
end

task :dist => [REPL_TARGET, UNIT_TEST_RUNNER_TARGET] do
  cp REPL_TARGET, "dist"
  cp UNIT_TEST_RUNNER_TARGET, "dist"
end

TRACE_SWF = "./bin/trace_swf.swf"
file TRACE_SWF => SHARED_SOURCES do
  options = COMPILE_OPTIONS + [$debug ? "-compiler.debug=true" : "", "-default-size 635 450"]
  sh "#{MXMLC} #{options.join(" ")} -file-specs src/as3/com/las3r/util/TraceSwf.as -output=#{TRACE_SWF}"
end

DEMO_SWF_ENTRY_POINTS.zip(DEMO_SWF_TARGETS).each do |pair|
  main, target = pair
  file target => SHARED_SOURCES - DEMO_SWF_ENTRY_POINTS + [main] do
    options = COMPILE_OPTIONS + [$debug ? "-compiler.debug=true" : "", "-default-size 635 450"]
    sh "#{MXMLC} #{options.join(" ")} -file-specs #{main} -output=#{target}"
  end
end

task :swc => [SWC_TARGET] do
end

task :repl => [REPL_TARGET] do
  sh "#{DEBUG_PROJECTOR} #{REPL_TARGET}"
end

task :lisp_units => [LISP_UNITS_TARGET] do
  sh "#{DEBUG_PROJECTOR} #{LISP_UNITS_TARGET}"
end

task :demos => DEMO_SWF_TARGETS do end

task :trace_swf => [TRACE_SWF] do end

task :units => [UNIT_TEST_RUNNER_TARGET] do
  sh "#{DEBUG_PROJECTOR} #{UNIT_TEST_RUNNER_TARGET}"
end

task :clean => [] do
  rm_rf UNIT_TEST_RUNNER_TARGET
end

task :default => [:units]












